# Java后端编码规范

## 作者信息
**作者**: kk
**创建日期**: 2025-10-01
**规范版本**: 1.0.0
**基于**: 阿里巴巴 Java 开发规范(嵩山版) v1.7.0

---

## 目录
1. [Java版本与特性](#java版本与特性)
2. [项目构建配置](#项目构建配置)
3. [Gradle编译检查约束](#gradle编译检查约束)
4. [代码风格规范](#代码风格规范)
   - 4.6 [日志使用规范](#46-日志使用规范) - 禁止使用System.out.println ⭐
5. [依赖管理规范](#依赖管理规范)
6. [测试规范](#测试规范)

## 相关规范文档
- [java21-standards.md](./java21-standards.md) - Java 21特性使用规范
- [gradle-standards.md](./gradle-standards.md) - Gradle构建规范
- [maven-standards.md](./maven-standards.md) - Maven构建规范
- [mybatis-flex-standards.md](./mybatis-flex-standards.md) - MyBatis-Flex使用规范

---

## 1. Java版本与特性

### 1.1 Java版本要求
- **最低版本**: Java 21
- **源代码兼容性**: Java 21
- **目标字节码**: Java 21

**详细规范**: 请参考 [java21-standards.md](./java21-standards.md)

### 1.2 推荐使用的Java 21特性
优先使用以下现代Java特性：

- **Virtual Threads (虚拟线程)**: 异步服务使用 `@Async("virtualThreadExecutor")`
- **Record Classes (记录类)**: 所有DTO/Request/Response使用Record定义
- **Pattern Matching (模式匹配)**: 复杂业务逻辑使用switch表达式
- **Sealed Classes (密封类)**: 限定继承层次结构
- **Text Blocks (文本块)**: 多行字符串使用文本块语法

**更多详情**: 请参考 [java21-standards.md](./java21-standards.md)

---

## 2. 项目构建配置

本项目支持Gradle和Maven两种构建工具:
- **Gradle配置**: 请参考 [gradle-standards.md](./gradle-standards.md)
- **Maven配置**: 请参考 [maven-standards.md](./maven-standards.md)

### 2.1 基本要求
- **Java版本**: 21
- **源代码编码**: UTF-8
- **资源文件编码**: UTF-8
- **编译器参数**: 必须包含 `-parameters` 以保留方法参数名

### 2.2 注解处理器
项目使用以下注解处理器：
- **Lombok**: 简化样板代码
- **MapStruct Plus**: 对象映射

**配置详情**:
- Gradle: 参考 [gradle-standards.md#5-注解处理器配置](./gradle-standards.md)
- Maven: 参考 [maven-standards.md#51-maven-compiler-plugin](./maven-standards.md)
- MyBatis-Flex: 参考 [mybatis-flex-standards.md#2-依赖配置](./mybatis-flex-standards.md)

---

## 3. Gradle编译检查约束

### 3.1 VO类型校验任务 (validateVoTypes)

项目在编译时强制执行VO类型规范检查，确保前后端数据交互的一致性和类型安全。

#### 3.1.1 校验规则

**必须遵守**的类型约束：

| 禁止使用的类型 | 必须使用的类型 | 原因 |
|------------|------------|------|
| BigDecimal | String | 避免JSON序列化精度丢失 |
| Long (ID字段) | String | 避免JavaScript精度问题（JS最大安全整数2^53-1） |
| LocalDateTime | String (格式: yyyy-MM-dd HH:mm:ss) | 统一时间格式，避免时区问题 |
| LocalDate | String (格式: yyyy-MM-dd) | 统一日期格式 |
| LocalTime | String (格式: HH:mm:ss) | 统一时间格式 |
| Date | String | 避免时区和序列化问题 |
| Timestamp | String | 避免精度和时区问题 |
| Instant | String | 统一格式 |
| ZonedDateTime | String | 统一格式 |
| OffsetDateTime | String | 统一格式 |

#### 3.1.2 触发时机
- 在每次 `compileJava` 任务执行前自动触发
- 可手动执行: `gradle validateVoTypes`

#### 3.1.3 校验范围
- 所有以 `VO` 或 `Vo` 结尾的Java类
- 支持普通类 (Class) 和记录类 (Record)
- 检查路径: `**/src/main/java/**/*VO.java` 和 `**/src/main/java/**/*Vo.java`

#### 3.1.4 错误示例与修正

**错误示例**:
```java
// PaymentVO.java - 违反规范
public record PaymentVO(
    Long id,                    // 错误: Long ID应使用String
    BigDecimal amount,          // 错误: BigDecimal应使用String
    LocalDateTime createTime    // 错误: LocalDateTime应使用String
) {}
```

**正确示例**:
```java
// PaymentVO.java - 符合规范
public record PaymentVO(
    String id,                  // 正确: ID使用String
    String amount,              // 正确: 金额使用String
    String createTime           // 正确: 时间使用String (格式: yyyy-MM-dd HH:mm:ss)
) {}
```

#### 3.1.5 类型转换工具类

项目提供工具类进行类型转换：
- **工具类路径**: `plus.sbt.common.util.VoTypeConverter`
- **主要方法**:
  - `BigDecimal -> String`: `VoTypeConverter.formatAmount(BigDecimal)`
  - `Long -> String`: `String.valueOf(Long)`
  - `LocalDateTime -> String`: `VoTypeConverter.formatDateTime(LocalDateTime)`
  - `LocalDate -> String`: `VoTypeConverter.formatDate(LocalDate)`

**使用示例**:
```java
// Entity到VO的转换
Payment payment = paymentRepository.findById(id);
PaymentVO vo = new PaymentVO(
    String.valueOf(payment.getId()),                    // Long -> String
    VoTypeConverter.formatAmount(payment.getAmount()),  // BigDecimal -> String
    VoTypeConverter.formatDateTime(payment.getCreateTime())  // LocalDateTime -> String
);
```

#### 3.1.6 编译失败处理

当检测到违规类型时，编译将失败并显示详细错误信息：

```
============================================================
VO类型校验失败！发现 3 个问题:
============================================================

/path/to/PaymentVO.java:15 - id (Long ID) 应使用String
/path/to/PaymentVO.java:16 - amount (BigDecimal) 应使用String
/path/to/PaymentVO.java:17 - createTime (LocalDateTime) 应使用String

修改建议:
1. BigDecimal -> String (避免精度丢失)
2. Long ID -> String (避免JavaScript精度问题)
3. 时间类型 -> String (格式: yyyy-MM-dd HH:mm:ss)
4. 使用工具类: plus.sbt.common.util.VoTypeConverter
```

#### 3.1.7 跳过校验（不推荐）

如果在特殊场景下需要临时跳过校验：
```bash
# 跳过VO类型校验编译
gradle compileJava -x validateVoTypes

# 构建时跳过校验（不推荐）
gradle build -x validateVoTypes
```

**注意**: 生产代码不应跳过此校验！

### 3.2 JavaParser AST分析

校验任务使用JavaParser进行精确的抽象语法树(AST)分析：
- **版本**: JavaParser 3.27.0
- **语言级别**: Java 21
- **分析范围**: 字段声明、记录参数
- **优势**: 准确识别类型、字段名、行号等信息

配置示例：
```groovy
def config = new com.github.javaparser.ParserConfiguration()
config.setLanguageLevel(com.github.javaparser.ParserConfiguration.LanguageLevel.JAVA_21)
com.github.javaparser.StaticJavaParser.setConfiguration(config)
```

---

## 4. 代码风格规范

### 4.1 命名规范

#### 4.1.1 类命名
- **Entity**: 实体类，对应数据库表（如 `Payment.java`）
- **VO (Value Object)**: 前端数据传输对象，必须符合VO类型规范（如 `PaymentVO.java`）
- **DTO (Data Transfer Object)**: 后端服务间数据传输（如 `PaymentDto.java`）
- **Request**: 请求参数对象（如 `CreatePaymentRequest.java`）
- **Response**: 响应结果对象（如 `PaymentResponse.java`）
- **Enum**: 枚举类，以 `Enum` 结尾（如 `PaymentStatusEnum.java`, `OrderTypeEnum.java`）
- **Service**: 业务逻辑服务（如 `PaymentService.java`）
- **ServiceImpl**: 服务实现类（如 `PaymentServiceImpl.java`）
- **Repository**: 数据访问接口（如 `PaymentRepository.java`）
- **Controller**: 控制器（如 `PaymentController.java`）
- **Manager**: 服务管理器，协调多个服务（如 `PaymentManager.java`）
- **Converter**: 转换器类（如 `PaymentConverter.java`）
- **Mapper**: MyBatis映射接口（如 `PaymentMapper.java`）
- **Config**: 配置类（如 `RedisConfig.java`, `WebMvcConfig.java`）
- **Exception**: 自定义异常类（如 `PaymentException.java`, `BusinessException.java`）
- **Constant**: 常量类（如 `PaymentConstant.java`, `SystemConstant.java`）

#### 4.1.2 字段命名
- 使用驼峰命名法（camelCase）
- ID字段统一使用 `id` 或以 `Id` 结尾（如 `orderId`, `userId`）
- 布尔字段以 `is`、`has`、`can` 开头（如 `isActive`, `hasPermission`）
- 时间字段以 `Time` 结尾（如 `createTime`, `updateTime`）

#### 4.1.3 方法命名
- **查询**: `find*`, `get*`, `query*`
- **创建**: `create*`, `add*`, `insert*`
- **更新**: `update*`, `modify*`
- **删除**: `delete*`, `remove*`
- **校验**: `validate*`, `check*`
- **转换**: `convert*`, `to*`, `from*`

### 4.2 Lombok使用规范

#### 4.2.1 推荐使用的注解
- `@Data`: 生成getter/setter/toString/equals/hashCode
- `@Builder`: 构建者模式
- `@AllArgsConstructor`: 全参数构造器
- `@NoArgsConstructor`: 无参构造器
- `@RequiredArgsConstructor`: 必需参数构造器
- `@Slf4j`: 日志对象

#### 4.2.2 避免使用
- `@SneakyThrows`: 隐藏异常，降低代码可读性
- `@Synchronized`: 使用Java原生synchronized更清晰

**示例**:
```java
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Slf4j
public class Payment {
    private Long id;
    private BigDecimal amount;
    private PaymentStatus status;
}
```

### 4.3 Record类使用规范

优先使用Record定义不可变数据对象：
- **适用场景**: DTO、VO、Request、Response
- **优势**: 简洁、不可变、自动生成equals/hashCode/toString

**示例**:
```java
// VO定义 - 遵守类型规范
public record PaymentVO(
    String id,
    String amount,
    String status,
    String createTime
) {}

// Request定义
public record CreatePaymentRequest(
    Long orderId,
    BigDecimal amount,
    String paymentCompany
) {}
```

### 4.4 Result和PageResult使用规范

**核心原则**: **禁止使用 `Result` 嵌套 `PageResult`**,分页查询直接返回 `PageResult`

#### 4.4.1 使用场景

- **Result**: 用于普通API响应,包含成功/失败状态、消息和数据
- **PageResult**: 用于分页查询API响应,包含分页信息和数据列表,**不需要再包装Result**

#### 4.4.2 正确示例

```java
// ✅ 正确: 普通查询返回Result
@GetMapping("/payment/{id}")
public Result<PaymentVO> getPayment(@PathVariable String id) {
    PaymentVO payment = paymentService.getById(id);
    return Result.success(payment);
}

// ✅ 正确: 分页查询直接返回PageResult
@GetMapping("/payments")
public PageResult<PaymentVO> pageQuery(@RequestParam int pageNum, @RequestParam int pageSize) {
    return paymentService.pageQuery(pageNum, pageSize);
}

// ✅ 正确: Service层直接返回PageResult
@Service
public class PaymentServiceImpl implements PaymentService {
    public PageResult<PaymentVO> pageQuery(int pageNum, int pageSize) {
        Page<Payment> page = paymentMapper.paginate(new Page<>(pageNum, pageSize), queryWrapper);

        List<PaymentVO> voList = page.getRecords().stream()
                .map(this::convertToVO)
                .toList();

        return new PageResult<>(
                page.getPageNumber(),
                page.getPageSize(),
                page.getTotalRow(),
                voList
        );
    }
}
```

#### 4.4.3 错误示例

```java
// ❌ 错误1: 不要使用Result包装PageResult
@GetMapping("/payments")
public Result<PageResult<PaymentVO>> pageQuery(@RequestParam int pageNum, @RequestParam int pageSize) {
    PageResult<PaymentVO> pageResult = paymentService.pageQuery(pageNum, pageSize);
    return Result.success(pageResult);  // 错误: 不需要Result包装
}

// ❌ 错误2: Service层返回Result<PageResult>
@Service
public class PaymentServiceImpl implements PaymentService {
    public Result<PageResult<PaymentVO>> pageQuery(int pageNum, int pageSize) {
        // 错误: Service层不应该返回Result<PageResult>
        PageResult<PaymentVO> pageResult = new PageResult<>(...);
        return Result.success(pageResult);
    }
}

// ❌ 错误3: 多层嵌套
@GetMapping("/payments")
public Result<PageResult<PaymentVO>> pageQuery(@RequestParam int pageNum, @RequestParam int pageSize) {
    // 如果Service返回Result<PageResult>,Controller再包装Result
    // 会导致: Result<Result<PageResult<PaymentVO>>>
    Result<PageResult<PaymentVO>> result = paymentService.pageQuery(pageNum, pageSize);
    return Result.success(result);  // 多层嵌套!
}
```

#### 4.4.4 标准做法

**分页查询标准实现**:

```java
// Controller层: 直接返回PageResult
@RestController
@RequestMapping("/api/payments")
public class PaymentController {

    @Autowired
    private PaymentService paymentService;

    @GetMapping
    public PageResult<PaymentVO> pageQuery(
            @RequestParam(defaultValue = "1") int pageNum,
            @RequestParam(defaultValue = "10") int pageSize) {
        // 直接返回PageResult,不要包装Result
        return paymentService.pageQuery(pageNum, pageSize);
    }
}

// Service层: 返回PageResult
@Service
public class PaymentServiceImpl implements PaymentService {

    @Autowired
    private PaymentMapper paymentMapper;

    @Override
    public PageResult<PaymentVO> pageQuery(int pageNum, int pageSize) {
        QueryWrapper query = QueryWrapper.create()
                .select()
                .from(PAYMENT)
                .where(PAYMENT.IS_DELETED.eq(0))
                .orderBy(PAYMENT.CREATE_TIME.desc());

        Page<Payment> page = paymentMapper.paginate(new Page<>(pageNum, pageSize), query);

        List<PaymentVO> voList = page.getRecords().stream()
                .map(this::convertToVO)
                .toList();

        return new PageResult<>(
                page.getPageNumber(),
                page.getPageSize(),
                page.getTotalRow(),
                voList
        );
    }

    private PaymentVO convertToVO(Payment payment) {
        return new PaymentVO(
                String.valueOf(payment.getId()),
                VoTypeConverter.formatAmount(payment.getAmount()),
                payment.getStatus().getCode(),
                VoTypeConverter.formatDateTime(payment.getCreateTime())
        );
    }
}
```

#### 4.4.5 原因说明

1. **避免冗余包装**: PageResult已经包含了数据和分页信息,不需要Result的success/fail状态
2. **简化前端处理**: 前端直接解析PageResult即可,不需要先解析Result再解析PageResult
3. **语义清晰**: PageResult本身就表示一个成功的分页查询结果
4. **统一规范**:
   - 普通查询 → `Result<T>`
   - 分页查询 → `PageResult<T>`
   - **绝不使用** `Result<PageResult<T>>`

### 4.5 枚举类(Enum)使用规范

#### 4.5.1 命名规范
- **类名**: 以 `Enum` 结尾，使用大驼峰命名（如 `PaymentStatusEnum`, `OrderTypeEnum`）
- **枚举常量**: 全大写，单词间用下划线分隔（如 `WAITING_PAYMENT`, `PAID`, `CANCELLED`）
- **字段名**: 使用驼峰命名法（如 `code`, `description`, `displayName`）

#### 4.5.2 推荐结构
枚举类应包含以下元素：
- **枚举常量**: 定义所有可能的值
- **字段**: 通常包含 `code`（代码）和 `description`（描述）
- **构造器**: 私有构造器初始化字段
- **Getter方法**: 提供字段访问
- **静态工具方法**: 提供根据code查找枚举的方法（如 `fromCode`, `valueOf`）

#### 4.5.3 标准示例

**简单枚举（仅常量）**:
```java
public enum PaymentCompanyEnum {
    WECHAT,      // 微信支付
    ALIPAY,      // 支付宝
    UNION_PAY    // 银联
}
```

**完整枚举（推荐）**:
```java
@Getter
@AllArgsConstructor
public enum PaymentStatusEnum {
    WAITING_PAYMENT("WAITING_PAYMENT", "待支付"),
    PAID("PAID", "已支付"),
    CANCELLED("CANCELLED", "已取消"),
    REFUNDED("REFUNDED", "已退款"),
    FAILED("FAILED", "支付失败");

    private final String code;
    private final String description;

    /**
     * 根据code获取枚举
     */
    public static PaymentStatusEnum fromCode(String code) {
        for (PaymentStatusEnum status : values()) {
            if (status.code.equals(code)) {
                return status;
            }
        }
        throw new IllegalArgumentException("未知的支付状态: " + code);
    }

    /**
     * 根据code获取枚举（返回Optional）
     */
    public static Optional<PaymentStatusEnum> fromCodeSafe(String code) {
        return Arrays.stream(values())
                .filter(status -> status.code.equals(code))
                .findFirst();
    }
}
```

**业务逻辑枚举（高级用法）**:
```java
@Getter
@AllArgsConstructor
public enum OrderTypeEnum {
    NORMAL("NORMAL", "普通订单") {
        @Override
        public BigDecimal calculateDiscount(BigDecimal amount) {
            return amount; // 无折扣
        }
    },
    VIP("VIP", "VIP订单") {
        @Override
        public BigDecimal calculateDiscount(BigDecimal amount) {
            return amount.multiply(new BigDecimal("0.9")); // 9折
        }
    },
    WHOLESALE("WHOLESALE", "批发订单") {
        @Override
        public BigDecimal calculateDiscount(BigDecimal amount) {
            return amount.multiply(new BigDecimal("0.8")); // 8折
        }
    };

    private final String code;
    private final String description;

    /**
     * 抽象方法 - 每个枚举常量必须实现
     */
    public abstract BigDecimal calculateDiscount(BigDecimal amount);
}
```

#### 4.5.4 集合工具方法

为枚举提供便捷的集合转换方法：
```java
@Getter
@AllArgsConstructor
public enum PaymentTypeEnum {
    ONLINE("ONLINE", "在线支付"),
    OFFLINE("OFFLINE", "线下支付"),
    COD("COD", "货到付款");

    private final String code;
    private final String description;

    /**
     * 获取所有code列表
     */
    public static List<String> getAllCodes() {
        return Arrays.stream(values())
                .map(PaymentTypeEnum::getCode)
                .collect(Collectors.toList());
    }

    /**
     * 获取所有枚举的Map（code -> enum）
     */
    public static Map<String, PaymentTypeEnum> getCodeMap() {
        return Arrays.stream(values())
                .collect(Collectors.toMap(
                    PaymentTypeEnum::getCode,
                    Function.identity()
                ));
    }

    /**
     * 检查code是否有效
     */
    public static boolean isValidCode(String code) {
        return Arrays.stream(values())
                .anyMatch(e -> e.code.equals(code));
    }
}
```

#### 4.5.5 数据库枚举映射

**注意**: 使用MyBatis-Flex时的枚举映射请参考 [mybatis-flex-standards.md#5-枚举映射](./mybatis-flex-standards.md)

#### 4.5.6 JSON序列化配置

**使用Jackson序列化为code值**:
```java
@Getter
@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // 序列化为字符串
public enum PaymentStatusEnum {
    WAITING_PAYMENT("WAITING_PAYMENT", "待支付"),
    PAID("PAID", "已支付");

    @JsonValue  // 指定此字段用于JSON序列化
    private final String code;
    private final String description;

    @JsonCreator  // 指定反序列化方法
    public static PaymentStatusEnum fromCode(String code) {
        return Arrays.stream(values())
                .filter(e -> e.code.equals(code))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("未知状态: " + code));
    }
}
```

#### 4.5.7 枚举最佳实践

1. **优先使用枚举而非常量类**: 枚举提供类型安全和编译时检查
2. **避免使用枚举的ordinal()**: 使用显式的code字段，ordinal()依赖声明顺序
3. **提供fromCode方法**: 便于字符串到枚举的转换
4. **使用Lombok简化代码**: `@Getter`、`@AllArgsConstructor` 减少样板代码
5. **考虑扩展性**: 新增枚举值不应破坏现有逻辑
6. **文档注释**: 每个枚举常量添加清晰的注释说明业务含义

**反例（不推荐）**:
```java
// ❌ 使用ordinal()
if (status.ordinal() == 0) { ... }

// ❌ 硬编码字符串比较
if ("PAID".equals(statusStr)) { ... }

// ❌ 缺少code字段，直接使用name()
enum Status { WAITING, PAID }  // name()不可控
```

**正例（推荐）**:
```java
// ✅ 使用枚举类型判断
if (status == PaymentStatusEnum.PAID) { ... }

// ✅ 使用code字段
if (status.getCode().equals("PAID")) { ... }

// ✅ 使用fromCode转换
PaymentStatusEnum status = PaymentStatusEnum.fromCode(statusStr);
```

### 4.6 日志使用规范

#### 4.6.1 禁止使用System.out.println

**强制规范**: **禁止在生产代码中使用 `System.out.println()` 和 `System.err.println()`**

**原因**:
1. **性能问题**: System.out是同步的,在高并发场景下会成为性能瓶颈
2. **无法控制级别**: 无法根据环境动态调整日志输出级别
3. **难以追踪**: 无法记录调用位置、线程信息、时间戳等上下文
4. **无法持久化**: 标准输出难以统一收集和归档
5. **生产问题**: 在生产环境中难以定位问题

#### 4.6.2 使用SLF4J + Logback

**推荐做法**: 使用 `@Slf4j` 注解配合日志框架

```java
// ❌ 错误: 使用System.out.println
public class PaymentService {
    public void processPayment(Long orderId) {
        System.out.println("开始处理订单支付: " + orderId);
        // 业务逻辑...
        System.out.println("订单支付处理完成: " + orderId);
    }
}

// ❌ 错误: 使用System.err.println
public class PaymentService {
    public void processPayment(Long orderId) {
        try {
            // 业务逻辑...
        } catch (Exception e) {
            System.err.println("支付处理失败: " + e.getMessage());
        }
    }
}
```

```java
// ✅ 正确: 使用@Slf4j日志
@Slf4j
@Service
public class PaymentService {

    public void processPayment(Long orderId) {
        log.info("开始处理订单支付, orderId: {}", orderId);

        try {
            // 业务逻辑...
            log.info("订单支付处理完成, orderId: {}", orderId);
        } catch (Exception e) {
            log.error("支付处理失败, orderId: {}", orderId, e);
        }
    }
}
```

#### 4.6.3 日志级别规范

根据不同场景选择合适的日志级别:

```java
@Slf4j
public class PaymentService {

    public void processPayment(Long orderId) {
        // TRACE: 追踪级别,最详细的信息(一般不用)
        log.trace("进入processPayment方法, orderId: {}", orderId);

        // DEBUG: 调试信息,开发阶段使用
        log.debug("查询订单信息, orderId: {}", orderId);

        // INFO: 一般信息,记录重要的业务流程
        log.info("开始处理订单支付, orderId: {}", orderId);

        // WARN: 警告信息,潜在问题但不影响运行
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            log.warn("订单金额异常, orderId: {}, amount: {}", orderId, amount);
        }

        // ERROR: 错误信息,业务异常或系统错误
        try {
            // 业务逻辑...
        } catch (PaymentException e) {
            log.error("支付处理失败, orderId: {}, errorCode: {}",
                     orderId, e.getErrorCode(), e);
            throw e;
        }
    }
}
```

#### 4.6.4 日志输出最佳实践

**1. 使用占位符,避免字符串拼接**:
```java
// ❌ 错误: 字符串拼接
log.info("订单支付: " + orderId + ", 金额: " + amount);

// ✅ 正确: 使用占位符
log.info("订单支付, orderId: {}, amount: {}", orderId, amount);
```

**2. 记录异常时包含堆栈信息**:
```java
// ❌ 错误: 只记录异常消息
catch (Exception e) {
    log.error("支付失败: " + e.getMessage());
}

// ✅ 正确: 记录完整堆栈
catch (Exception e) {
    log.error("支付失败, orderId: {}", orderId, e);
}
```

**3. 避免敏感信息泄露**:
```java
// ❌ 错误: 记录敏感信息
log.info("用户登录, password: {}", password);
log.info("支付请求, cardNumber: {}", cardNumber);

// ✅ 正确: 脱敏处理
log.info("用户登录, username: {}", username);
log.info("支付请求, cardNumber: {}****", cardNumber.substring(0, 4));
```

**4. 条件日志输出**:
```java
// 对于复杂的日志内容,先判断级别再输出
if (log.isDebugEnabled()) {
    log.debug("复杂对象详情: {}", expensiveToString(obj));
}
```

#### 4.6.5 唯一允许的例外场景

以下场景可以临时使用 `System.out.println`,但**不允许提交到生产代码**:

1. **本地开发调试**: 临时调试代码,调试完成后必须删除
2. **Main方法**: 应用启动入口的简单信息输出
3. **命令行工具**: 专门的CLI工具需要向控制台输出结果

```java
// ✅ 允许: Main方法中的启动信息
public class Application {
    public static void main(String[] args) {
        System.out.println("应用启动中...");
        SpringApplication.run(Application.class, args);
    }
}

// ✅ 允许: CLI工具的输出
public class DataExportTool {
    public static void main(String[] args) {
        System.out.println("导出数据到: " + outputFile);
        // 导出逻辑...
    }
}
```

#### 4.6.6 代码检查

建议在代码审查时检查:
- [ ] 没有使用 `System.out.println`
- [ ] 没有使用 `System.err.println`
- [ ] 所有业务类使用 `@Slf4j` 注解
- [ ] 日志级别选择合适
- [ ] 异常日志包含堆栈信息
- [ ] 没有记录敏感信息

---

## 5. 依赖管理规范

### 5.1 核心框架版本

```gradle
ext {
    springBootVersion = '3.4.7'
    saTokenVersion = '1.38.0'
    mapstructPlusVersion = '1.4.8'
    hutoolVersion = '5.8.38'
    weixinJavaPayVersion = '4.7.7.B'
    nettyVersion = '4.2.5.Final'
}
```

**注意**: MyBatis-Flex依赖配置请参考 [mybatis-flex-standards.md#2-依赖配置](./mybatis-flex-standards.md)

### 5.2 依赖管理原则
1. **统一版本管理**: 在根项目的 `ext` 块或 `dependencyManagement` 中定义版本号
2. **避免版本冲突**: 使用 `mavenBom` 统一管理Spring Boot和Netty依赖
3. **最小化依赖**: 只引入必需的依赖，避免传递依赖污染
4. **本地仓库优先**: 配置 `mavenLocal()` 优先使用本地构建的模块

### 5.3 仓库配置顺序
```gradle
repositories {
    mavenLocal()      // 本地仓库优先
    mavenCentral()    // Maven中央仓库
    maven {           // 阿里云镜像加速
        url = 'https://maven.aliyun.com/repository/public/'
        name = 'aliyun'
    }
}
```

---

## 6. 测试规范

本项目的Java测试规范已独立为单独文档,请参考:

**详细测试规范**: [java-test-standards.md](./java-test-standards.md)

### 6.1 核心要点

- **测试框架**: JUnit 5 + Mockito + Spring Boot Test
- **Mock注解**: 禁止使用过时的 `@MockBean`,必须使用 `@MockitoBean`
- **覆盖率要求**: 单元测试 ≥ 80%,集成测试 ≥ 70%,API接口 100%
- **命名规范**: `should_<预期结果>_when_<测试条件>`

### 6.2 快速索引

| 测试场景 | 查看章节 |
|---------|---------|
| Mock注解使用(重要) | [java-test-standards.md#3-mock注解使用规范](./java-test-standards.md) |
| 单元测试规范 | [java-test-standards.md#6-单元测试规范](./java-test-standards.md) |
| 集成测试规范 | [java-test-standards.md#7-集成测试规范](./java-test-standards.md) |
| 测试最佳实践 | [java-test-standards.md#8-测试最佳实践](./java-test-standards.md) |
| 参数化测试 | [java-test-standards.md#63-参数化测试](./java-test-standards.md) |
| 测试覆盖率配置 | [java-test-standards.md#42-jacoco配置](./java-test-standards.md) |

---

## 7. Maven发布规范

### 7.1 发布配置
项目所有子模块支持发布到Maven仓库：

```gradle
publishing {
    publications {
        maven(MavenPublication) {
            from components.java

            pom {
                name = project.name
                description = 'SBT Plus Framework - ' + project.name
                url = 'https://github.com/sbt-plus/sbt-plus'

                licenses {
                    license {
                        name = 'The Apache License, Version 2.0'
                        url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
            }
        }
    }
}

java {
    withSourcesJar()  // 生成源码包
}
```

### 7.2 发布命令
```bash
# 发布到本地Maven仓库
gradle publishToMavenLocal

# 发布所有模块
gradle publish
```

---

## 8. 构建优化

### 8.1 Gradle 9.0优化配置
```gradle
// 启用完整堆栈跟踪
gradle.startParameter.showStacktrace = ShowStacktrace.ALWAYS_FULL

// 配置缓存和构建缓存
// gradle.properties中配置:
// org.gradle.caching=true
// org.gradle.configuration-cache=true
```

### 8.2 快速编译命令
```bash
# 跳过测试快速编译
gradle compileJava

# 跳过测试快速构建
gradle build -x test

# 仅编译特定模块
gradle :sbt-plus-payment:sbt-plus-payment-core:compileJava
```

---

## 9. 常见问题排查

### 9.1 VO类型校验失败
**问题**: 编译时提示VO类型不符合规范  
**解决**:
1. 检查错误信息中的文件路径和行号
2. 将 `BigDecimal`、`Long ID`、时间类型改为 `String`
3. 使用 `VoTypeConverter` 工具类进行类型转换

### 9.2 注解处理器不生效
**问题**: Lombok、MapStruct等注解未生成代码  
**解决**:
1. 确认 `annotationProcessor` 依赖已正确配置
2. 执行 `gradle clean compileJava` 重新生成
3. IDEA用户: 启用 `Settings -> Build -> Compiler -> Annotation Processors`

### 9.3 依赖冲突
**问题**: 不同模块引入了相同依赖的不同版本  
**解决**:
1. 使用 `gradle dependencies` 查看依赖树
2. 在 `dependencyManagement` 中统一版本
3. 使用 `exclude` 排除冲突的传递依赖

---

## 10. 参考资料

- [Java 21官方文档](https://docs.oracle.com/en/java/javase/21/)
- [Spring Boot 3.4.7文档](https://docs.spring.io/spring-boot/docs/3.4.7/reference/html/)
- [MyBatis-Flex官方文档](https://mybatis-flex.com/)
- [Gradle 9.0用户指南](https://docs.gradle.org/9.0/userguide/userguide.html)
- [Lombok功能特性](https://projectlombok.org/features/)
- [MapStruct Plus文档](https://github.com/linpeilie/mapstruct-plus)

---

**最后更新**: 2025-10-05
**维护者**: kk
