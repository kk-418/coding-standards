# Debug调试规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **用途**: 规范调试问题的方法和流程,避免盲目猜测

---

## 核心原则

### ⚠️ 禁止猜测式调试

**绝对禁止**:
- ❌ 凭感觉猜测问题原因
- ❌ 没有证据就修改代码
- ❌ 盲目尝试各种解决方案
- ❌ 随意添加或删除代码来"试试看"

**必须遵守**:
- ✅ 基于证据进行分析
- ✅ 通过日志定位问题
- ✅ 逐步缩小问题范围
- ✅ 验证每个假设

---

## 调试流程

### 1. 收集现有证据

**必须收集的信息**:
```
□ 错误日志/异常堆栈
□ 复现步骤
□ 输入参数
□ 预期结果 vs 实际结果
□ 系统环境信息
□ 相关代码版本
```

**示例**:
```
问题描述: 支付接口返回500错误
错误信息: NullPointerException at PaymentService.java:125
输入参数: orderId=12345, amount=100.00
预期结果: 返回支付成功
实际结果: 服务器内部错误
环境: 测试环境, Java 21, Spring Boot 3.4.7
```

### 2. 分析证据,提出假设

基于现有证据,列出可能的原因:

```markdown
## 可能的原因分析

1. NullPointerException在PaymentService.java:125
   - 可能是payment对象为null
   - 可能是payment的某个属性为null
   - 可能是依赖的service未注入

2. 需要验证的假设
   - [ ] payment对象是否正确查询出来
   - [ ] payment.getAmount()是否为null
   - [ ] paymentGatewayService是否注入成功
```

### 3. 通过日志验证假设

**当现有证据不足时,必须添加日志**:

```java
@Service
public class PaymentService {

    public PaymentResult process(Long orderId, BigDecimal amount) {
        // 添加日志: 验证输入参数
        log.info("开始处理支付: orderId={}, amount={}", orderId, amount);

        // 添加日志: 验证查询结果
        Payment payment = paymentRepository.findById(orderId);
        log.info("查询到的支付记录: {}", payment);

        if (payment == null) {
            log.error("支付记录不存在: orderId={}", orderId);
            throw new PaymentNotFoundException("支付记录不存在");
        }

        // 添加日志: 验证对象状态
        log.info("支付记录状态: status={}, amount={}",
                payment.getStatus(), payment.getAmount());

        // 第125行 - 之前抛NPE的地方
        BigDecimal totalAmount = payment.getAmount().add(amount);
        log.info("计算后的总金额: {}", totalAmount);

        return new PaymentResult("SUCCESS");
    }
}
```

### 4. 执行调试,收集新证据

运行代码,查看日志输出:

```
2025-10-01 10:30:00 INFO  开始处理支付: orderId=12345, amount=100.00
2025-10-01 10:30:00 INFO  查询到的支付记录: Payment(id=12345, status=PENDING, amount=null)
2025-10-01 10:30:00 INFO  支付记录状态: status=PENDING, amount=null
2025-10-01 10:30:00 ERROR NullPointerException at PaymentService.java:125
```

**分析新证据**:
- ✅ payment对象不为null (已确认)
- ✅ payment对象能正常查询出来 (已确认)
- ⚠️ **payment.getAmount()为null** (找到根因!)

### 5. 定位根本原因

基于证据链,定位到根本原因:

```markdown
## 根本原因

payment.getAmount()为null导致NPE

## 为什么amount为null?

需要进一步调查:
1. 数据库中amount字段是否为null?
2. 实体类映射是否正确?
3. 是否有其他代码将amount设置为null?
```

### 6. 继续深入调查

添加更多日志或查询数据库:

```sql
-- 查询数据库原始数据
SELECT id, amount, status, create_time
FROM payment
WHERE id = 12345;

-- 结果: amount字段在数据库中确实为NULL
```

**最终确认**:
- 数据库中amount字段为NULL
- 原因: 创建支付记录时未设置amount
- 解决方案: 修复创建逻辑,确保amount不为null

---

## 日志添加规范

### 3.1 关键位置必须打日志

```java
// 1. 方法入口 - 记录输入参数
log.info("方法开始: method={}, params={}", "processPayment", params);

// 2. 重要查询 - 记录查询结果
Payment payment = paymentRepository.findById(id);
log.info("查询结果: payment={}", payment);

// 3. 业务判断 - 记录判断条件和结果
if (payment.getStatus() == PaymentStatus.PAID) {
    log.info("支付已完成,跳过处理: paymentId={}", payment.getId());
    return;
}

// 4. 外部调用 - 记录请求和响应
log.info("调用支付网关: request={}", gatewayRequest);
GatewayResponse response = paymentGateway.pay(gatewayRequest);
log.info("支付网关响应: response={}", response);

// 5. 异常情况 - 记录错误信息
catch (Exception e) {
    log.error("支付处理失败: paymentId={}, error={}", payment.getId(), e.getMessage(), e);
    throw e;
}

// 6. 方法结束 - 记录返回结果
log.info("方法结束: result={}", result);
return result;
```

### 3.2 日志级别使用规范

```java
// TRACE: 最详细的信息,开发调试用
log.trace("进入方法: method={}, line={}", "processPayment", 125);

// DEBUG: 调试信息,帮助诊断问题
log.debug("中间变量值: totalAmount={}, fee={}", totalAmount, fee);

// INFO: 重要的业务流程信息
log.info("支付处理完成: paymentId={}, status={}", paymentId, status);

// WARN: 警告信息,不影响运行但需要关注
log.warn("支付金额异常: amount={}, 建议检查", amount);

// ERROR: 错误信息,影响功能正常运行
log.error("支付失败: paymentId={}, error={}", paymentId, e.getMessage(), e);
```

### 3.3 日志内容规范

**好的日志**:
```java
// ✅ 包含关键信息,便于定位
log.info("支付处理: orderId={}, amount={}, status={}",
         orderId, amount, status);

// ✅ 包含上下文信息
log.error("调用支付网关失败: paymentId={}, gateway={}, error={}",
          paymentId, gatewayName, e.getMessage(), e);

// ✅ 使用占位符,而非字符串拼接
log.info("用户登录: userId={}, ip={}", userId, ip);
```

**不好的日志**:
```java
// ❌ 信息不足,无法定位问题
log.info("处理支付");

// ❌ 使用字符串拼接,性能差
log.info("支付处理: " + orderId + ", " + amount);

// ❌ 日志过于啰嗦,淹没关键信息
log.info("开始处理支付,首先查询订单信息,然后验证订单状态...");
```

---

## 调试案例

### 案例1: API返回500错误

**❌ 错误的调试方式**:
```
问题: API返回500
猜测: 可能是数据库连接问题
行动: 重启数据库 -> 问题依旧
猜测: 可能是缓存问题
行动: 清空Redis -> 问题依旧
猜测: 可能是代码bug
行动: 随机修改代码 -> 问题依旧
结果: 浪费大量时间,问题未解决
```

**✅ 正确的调试方式**:
```
1. 收集证据:
   - 查看错误日志: NullPointerException at PaymentService.java:125
   - 查看堆栈: payment.getAmount().add(fee)

2. 分析证据:
   - NPE说明某个对象为null
   - 可能是payment为null,或payment.getAmount()为null

3. 添加日志验证:
   log.info("payment对象: {}", payment);
   log.info("payment.getAmount(): {}", payment.getAmount());

4. 重新测试,查看日志:
   payment对象: Payment(id=123, amount=null)

5. 确认根因:
   - payment.getAmount()返回null
   - 查询数据库,发现amount字段确实为null

6. 修复问题:
   - 在创建支付时确保amount不为null
   - 添加非空校验
```

### 案例2: 数据查询结果为空

**❌ 错误的调试方式**:
```
问题: 查询不到数据
猜测: 可能是SQL写错了
行动: 修改SQL -> 问题依旧
猜测: 可能是数据库没数据
行动: 手动插入数据 -> 能查到了
结论: 数据库没数据(错误结论,掩盖了真正问题)
```

**✅ 正确的调试方式**:
```
1. 收集证据:
   - 查询条件: WHERE user_id = 12345
   - 返回结果: 空列表

2. 添加日志:
   log.info("执行查询: userId={}", userId);
   List<Order> orders = orderMapper.findByUserId(userId);
   log.info("查询结果: count={}, orders={}", orders.size(), orders);

3. 查看日志:
   执行查询: userId=12345
   查询结果: count=0, orders=[]

4. 直接查数据库:
   SELECT * FROM orders WHERE user_id = 12345;
   -- 结果: 有10条数据

5. 对比发现:
   - 数据库有数据,但查询不到
   - 可能是查询条件有问题

6. 打印实际SQL:
   -- 开启MyBatis日志
   Preparing: SELECT * FROM orders WHERE user_id = ? AND is_deleted = 1
   Parameters: 12345(Long)

7. 找到根因:
   - SQL中多了 is_deleted = 1 的条件
   - 这些订单的is_deleted都是0(未删除)

8. 修复:
   - 修改查询条件为 is_deleted = 0
```

---

## 调试工具

### 4.1 日志工具

```java
// SLF4J + Logback (推荐)
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class PaymentService {
    private static final Logger log = LoggerFactory.getLogger(PaymentService.class);

    public void process() {
        log.info("处理支付");
    }
}

// 或使用Lombok
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Service
public class PaymentService {
    public void process() {
        log.info("处理支付");
    }
}
```

### 4.2 调试配置

**application.yml**:
```yaml
# 开启SQL日志
logging:
  level:
    # MyBatis SQL日志
    com.mybatisflex: DEBUG
    # 业务日志
    plus.sbt: DEBUG
    # Spring框架日志
    org.springframework: INFO

# MyBatis-Flex配置
mybatis-flex:
  configuration:
    # 打印SQL
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

### 4.3 IDE调试

使用断点调试时,观察:
```
1. 变量值
2. 对象状态
3. 方法调用栈
4. 线程状态
```

---

## 调试检查清单

### 开始调试前

```
□ 确认问题能稳定复现
□ 收集完整的错误信息
□ 了解预期行为和实际行为的差异
□ 确认最近的代码变更
```

### 调试过程中

```
□ 每个假设都要用证据验证
□ 证据不足时添加日志再测试
□ 记录每一步的发现
□ 避免一次性修改多个地方
```

### 问题解决后

```
□ 确认根本原因已找到
□ 验证修复方案解决了问题
□ 编写测试用例防止回归
□ 移除临时添加的调试日志
□ 文档记录问题和解决方案
```

---

## 总结

### 调试黄金法则

1. **基于证据,不靠猜测**
   - 所有结论必须有证据支撑
   - 证据不足就添加日志收集更多信息

2. **逐步缩小范围**
   - 从大范围到小范围
   - 从表象到根因
   - 一步步验证假设

3. **系统性思考**
   - 考虑数据流向
   - 考虑调用链路
   - 考虑边界条件

4. **记录调试过程**
   - 记录每一步的发现
   - 记录验证的假设
   - 形成问题解决的知识库

---

**最后更新**: 2025-10-01
**维护者**: kk
