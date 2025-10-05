# MyBatis-Flex使用规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: MyBatis-Flex 1.11.1 + Spring Boot项目

---

## 目录
1. [MyBatis-Flex简介](#mybatis-flex简介)
2. [依赖配置](#依赖配置)
3. [实体类规范](#实体类规范)
4. [Mapper接口规范](#mapper接口规范)
   - 4.4 [更新操作规范](#44-更新操作规范) ⭐
5. [枚举映射](#枚举映射)
6. [查询构造器(QueryWrapper)](#查询构造器querywrapper)
7. [分页查询](#分页查询)
8. [逻辑删除](#逻辑删除)
9. [乐观锁](#乐观锁)
10. [多数据源配置](#多数据源配置)
11. [代码生成器](#代码生成器)
12. [最佳实践](#最佳实践)

---

## 1. MyBatis-Flex简介

MyBatis-Flex是一个优雅的MyBatis增强框架，提供以下核心特性：
- 轻量级：仅增强不改变，引入它不会对现有工程产生影响
- 强大的CRUD操作：内置通用Mapper，轻松完成CRUD操作
- 支持多主键、逻辑删除、乐观锁等功能
- 支持Db + Row：无需实体类即可操作数据库
- QueryWrapper：强大的查询条件构造器
- 关联查询：一对一、一对多、多对多关联查询

**官方文档**: https://mybatis-flex.com/

---

## 2. 依赖配置

### 2.1 Gradle依赖

```gradle
ext {
    mybatisFlexVersion = '1.11.1'
}

dependencies {
    // MyBatis-Flex核心依赖
    implementation "com.mybatis-flex:mybatis-flex-spring-boot-starter:${mybatisFlexVersion}"

    // 注解处理器(APT) - 生成QueryWrapper
    annotationProcessor "com.mybatis-flex:mybatis-flex-processor:${mybatisFlexVersion}"

    // 代码生成器(可选)
    implementation "com.mybatis-flex:mybatis-flex-codegen:${mybatisFlexVersion}"
}
```

### 2.2 配置文件

**application.yml**:
```yaml
mybatis-flex:
  # 配置文件路径
  mapper-locations: classpath*:/mapper/**/*.xml
  # 类型别名包
  type-aliases-package: plus.sbt.*.entity
  # 全局配置
  global-config:
    # 逻辑删除字段
    logic-delete-column: is_deleted
    # 版本号字段(乐观锁)
    version-column: version
    # 主键类型(AUTO自增、SNOW_FLAKE雪花、UUID等)
    key-type: auto
  # 配置打印SQL
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

---

## 3. 实体类规范

### 3.1 基本实体类定义

```java
@Data
@Table("payment")
public class Payment {

    @Id(keyType = KeyType.Auto)  // 主键，自增
    private Long id;

    private BigDecimal amount;

    @Column("payment_status")  // 数据库字段名与实体字段名不一致时使用
    private PaymentStatusEnum status;

    @Column("order_id")
    private Long orderId;

    @Column(value = "create_time", onInsertValue = "now()")  // 插入时自动填充
    private LocalDateTime createTime;

    @Column(value = "update_time", onInsertValue = "now()", onUpdateValue = "now()")  // 插入和更新时自动填充
    private LocalDateTime updateTime;

    @Column(isLogicDelete = true)  // 逻辑删除字段
    private Integer isDeleted;

    @Column(version = true)  // 乐观锁版本号
    private Integer version;
}
```

### 3.2 常用注解说明

| 注解 | 说明 | 示例 |
|------|------|------|
| `@Table` | 指定表名 | `@Table("sys_user")` |
| `@Id` | 标识主键 | `@Id(keyType = KeyType.Auto)` |
| `@Column` | 指定列名或配置 | `@Column("user_name")` |
| `@Column(ignore = true)` | 忽略该字段 | 不映射到数据库 |
| `@Column(isLogicDelete = true)` | 逻辑删除字段 | 删除时设置为1 |
| `@Column(version = true)` | 乐观锁版本号 | 更新时自动+1 |
| `@Column(onInsertValue = "...")` | 插入时默认值 | `onInsertValue = "now()"` |
| `@Column(onUpdateValue = "...")` | 更新时默认值 | `onUpdateValue = "now()"` |

### 3.3 主键策略

```java
public enum KeyType {
    Auto,           // 数据库自增
    None,           // 无主键
    Generator,      // 自定义生成器
    Sequence,       // 数据库序列
}

// 示例
@Id(keyType = KeyType.Auto)  // MySQL自增主键
private Long id;

@Id(keyType = KeyType.Generator, value = "snowFlakeId")  // 雪花ID
private Long id;
```

### 3.4 字段自动填充

```java
@Data
@Table("payment")
public class Payment {

    // 插入时自动填充当前时间
    @Column(value = "create_time", onInsertValue = "now()")
    private LocalDateTime createTime;

    // 插入和更新时都填充当前时间
    @Column(value = "update_time", onInsertValue = "now()", onUpdateValue = "now()")
    private LocalDateTime updateTime;

    // 插入时自动填充创建人(需要配置MyBatisFlexCustomizer)
    @Column(value = "create_by", onInsertValue = "currentUser()")
    private String createBy;
}
```

---

## 4. Mapper接口规范

### 4.1 基础Mapper定义

```java
/**
 * Payment Mapper接口
 */
public interface PaymentMapper extends BaseMapper<Payment> {
    // 继承BaseMapper后，自动拥有CRUD方法
    // 无需写XML，除非有复杂查询
}
```

### 4.2 BaseMapper内置方法

MyBatis-Flex的`BaseMapper`提供了丰富的CRUD方法：

```java
// 插入
int insert(T entity);
int insertBatch(Collection<T> entities);
int insertSelective(T entity);  // 只插入非null字段

// 删除
int deleteById(Serializable id);
int deleteByIds(Collection<? extends Serializable> ids);
int deleteByMap(Map<String, Object> map);
int deleteByQuery(QueryWrapper query);

// 更新
int updateById(T entity);
int updateByQuery(T entity, QueryWrapper query);
int update(T entity, boolean ignoreNulls, QueryWrapper query);

// 查询
T selectOneById(Serializable id);
List<T> selectListByIds(Collection<? extends Serializable> ids);
List<T> selectListByMap(Map<String, Object> map);
List<T> selectListByQuery(QueryWrapper query);
T selectOneByQuery(QueryWrapper query);
long selectCountByQuery(QueryWrapper query);

// 分页查询
Page<T> paginate(Page<T> page, QueryWrapper query);
```

### 4.3 自定义查询方法

```java
public interface PaymentMapper extends BaseMapper<Payment> {

    /**
     * 根据订单ID查询支付记录
     */
    @Select("SELECT * FROM payment WHERE order_id = #{orderId} AND is_deleted = 0")
    List<Payment> selectByOrderId(@Param("orderId") Long orderId);

    /**
     * 统计某时间段内的支付总额
     */
    @Select("SELECT SUM(amount) FROM payment " +
            "WHERE create_time BETWEEN #{startTime} AND #{endTime} " +
            "AND payment_status = 'PAID'")
    BigDecimal sumAmountByTimeRange(@Param("startTime") LocalDateTime startTime,
                                     @Param("endTime") LocalDateTime endTime);
}
```

### 4.4 更新操作规范 ⭐

**核心原则**: 更新指定字段时,优先使用`UpdateChain`,而不是使用`updateById`更新全部字段。

#### 4.4.1 为什么使用UpdateChain

使用`updateById`更新全部字段存在以下问题:
1. **性能浪费**: 即使只需要更新1-2个字段,也会更新所有字段
2. **并发风险**: 可能覆盖其他线程的更新(除非使用乐观锁)
3. **SQL冗余**: 生成的SQL包含大量不必要的SET子句
4. **语义不清**: 代码意图不明确,不清楚到底要更新哪些字段

#### 4.4.2 正确示例: 使用UpdateChain

```java
@Service
@RequiredArgsConstructor
public class PaymentService {

    private final PaymentMapper paymentMapper;

    /**
     * 更新支付状态(推荐方式)
     */
    public void updatePaymentStatus(Long paymentId, PaymentStatusEnum newStatus) {
        UpdateChain.of(Payment.class)
                .set(Payment::getStatus, newStatus)
                .where(Payment::getId).eq(paymentId)
                .update();
    }

    /**
     * 更新多个字段
     */
    public void updatePaymentInfo(Long paymentId, BigDecimal amount, String remark) {
        UpdateChain.of(Payment.class)
                .set(Payment::getAmount, amount)
                .set(Payment::getRemark, remark)
                .where(Payment::getId).eq(paymentId)
                .update();
    }

    /**
     * 带条件的更新
     */
    public void updateStatusByOrderId(Long orderId, PaymentStatusEnum newStatus) {
        UpdateChain.of(Payment.class)
                .set(Payment::getStatus, newStatus)
                .where(Payment::getOrderId).eq(orderId)
                .and(Payment::getStatus).eq(PaymentStatusEnum.WAITING_PAYMENT)
                .update();
    }
}
```

生成的SQL:
```sql
-- 只更新需要的字段
UPDATE payment
SET status = 'PAID', update_time = now()
WHERE id = 123
```

#### 4.4.3 错误示例: 滥用updateById

```java
// ❌ 错误: 只需要更新status,却查询并更新了全部字段
public void updatePaymentStatus(Long paymentId, PaymentStatusEnum newStatus) {
    Payment payment = paymentMapper.selectOneById(paymentId);
    payment.setStatus(newStatus);
    paymentMapper.updateById(payment);
}
```

生成的SQL:
```sql
-- 更新了所有字段,即使大部分字段值没有变化
UPDATE payment
SET order_id = 456,
    amount = 100.00,
    status = 'PAID',          -- 只有这个字段需要更新
    remark = 'xxx',
    create_time = '2024-01-01 10:00:00',
    update_time = now(),
    version = 2
WHERE id = 123 AND version = 1
```

#### 4.4.4 updateById的适用场景

`updateById` 只在以下场景使用:
1. **表单编辑**: 用户通过表单修改了实体的大部分字段
2. **数据同步**: 需要完整替换某条记录的所有字段
3. **乐观锁更新**: 需要基于version字段的并发控制

```java
// ✅ 正确: 用户编辑表单,修改了多个字段
@Transactional(rollbackFor = Exception.class)
public void updatePaymentByForm(UpdatePaymentRequest request) {
    Payment payment = paymentMapper.selectOneById(request.getId());

    // 更新多个字段
    payment.setAmount(request.getAmount());
    payment.setStatus(request.getStatus());
    payment.setRemark(request.getRemark());
    payment.setPaymentMethod(request.getPaymentMethod());
    payment.setPaymentTime(request.getPaymentTime());

    // 使用updateById更新全部字段,并通过version实现乐观锁
    int rows = paymentMapper.updateById(payment);
    if (rows == 0) {
        throw new BusinessException("更新失败,数据已被修改");
    }
}
```

#### 4.4.5 UpdateChain高级用法

```java
// 条件更新 + 自增/自减
UpdateChain.of(Payment.class)
        .setRaw(Payment::getRetryCount, "retry_count + 1")  // 重试次数+1
        .set(Payment::getStatus, PaymentStatusEnum.RETRYING)
        .where(Payment::getId).eq(paymentId)
        .update();

// 动态字段更新
UpdateChain<Payment> updateChain = UpdateChain.of(Payment.class)
        .where(Payment::getId).eq(paymentId);

if (amount != null) {
    updateChain.set(Payment::getAmount, amount);
}
if (remark != null) {
    updateChain.set(Payment::getRemark, remark);
}

updateChain.update();
```

#### 4.4.6 性能对比

| 操作 | SQL字段数 | 数据库负载 | 网络传输 | 推荐度 |
|------|-----------|-----------|---------|--------|
| UpdateChain(1个字段) | 1-2个 | 低 | 小 | ⭐⭐⭐⭐⭐ |
| UpdateChain(2-3个字段) | 2-4个 | 低 | 小 | ⭐⭐⭐⭐⭐ |
| updateById(10个字段) | 10+个 | 中 | 中 | ⭐⭐ |
| updateById(20个字段) | 20+个 | 高 | 大 | ⭐ |

**规范总结**:
- ✅ **推荐**: 使用`UpdateChain`精确更新需要修改的字段
- ⚠️ **谨慎**: `updateById`仅用于表单编辑、数据同步等需要更新大部分字段的场景
- ❌ **禁止**: 使用`updateById`更新单个或少量字段

---

## 5. 枚举映射

### 5.1 使用@EnumValue注解

```java
@Getter
@AllArgsConstructor
public enum PaymentStatusEnum {
    WAITING_PAYMENT("WAITING_PAYMENT", "待支付"),
    PAID("PAID", "已支付"),
    CANCELLED("CANCELLED", "已取消");

    @EnumValue  // 标记此字段用于数据库存储
    private final String code;
    private final String description;
}
```

### 5.2 实体类中使用枚举

```java
@Data
@Table("payment")
public class Payment {
    private Long id;
    private BigDecimal amount;

    // 数据库存储为code值(如"PAID")，读取时自动转换为枚举
    private PaymentStatusEnum status;

    private LocalDateTime createTime;
}
```

### 5.3 全局枚举配置

```java
@Configuration
public class MyBatisFlexConfiguration {

    @Bean
    public MyBatisFlexCustomizer myBatisFlexCustomizer() {
        return new MyBatisFlexCustomizer() {
            @Override
            public void customize(FlexConfiguration configuration) {
                // 配置枚举类型处理器
                configuration.setDefaultEnumTypeHandler(EnumValue.class);
            }
        };
    }
}
```

---

## 6. 查询构造器(QueryWrapper)

### 6.1 基本查询

```java
@Service
@RequiredArgsConstructor
public class PaymentService {

    private final PaymentMapper paymentMapper;

    public List<Payment> findByStatus(PaymentStatusEnum status) {
        QueryWrapper query = QueryWrapper.create()
                .select()
                .from(PAYMENT)
                .where(PAYMENT.STATUS.eq(status))
                .and(PAYMENT.IS_DELETED.eq(0));

        return paymentMapper.selectListByQuery(query);
    }
}
```

### 6.2 复杂条件查询

```java
public Page<Payment> searchPayments(PaymentSearchRequest request, Page<Payment> page) {
    QueryWrapper query = QueryWrapper.create()
            .select()
            .from(PAYMENT)
            .where(PAYMENT.IS_DELETED.eq(0));

    // 动态条件：支付状态
    if (request.status() != null) {
        query.and(PAYMENT.STATUS.eq(request.status()));
    }

    // 动态条件：金额范围
    if (request.minAmount() != null) {
        query.and(PAYMENT.AMOUNT.ge(request.minAmount()));
    }
    if (request.maxAmount() != null) {
        query.and(PAYMENT.AMOUNT.le(request.maxAmount()));
    }

    // 动态条件：时间范围
    if (request.startTime() != null && request.endTime() != null) {
        query.and(PAYMENT.CREATE_TIME.between(request.startTime(), request.endTime()));
    }

    // 排序
    query.orderBy(PAYMENT.CREATE_TIME.desc());

    return paymentMapper.paginate(page, query);
}
```

### 6.3 常用查询方法

```java
// 等于
.where(PAYMENT.STATUS.eq("PAID"))

// 不等于
.where(PAYMENT.STATUS.ne("CANCELLED"))

// 大于/大于等于
.where(PAYMENT.AMOUNT.gt(new BigDecimal("100")))
.where(PAYMENT.AMOUNT.ge(new BigDecimal("100")))

// 小于/小于等于
.where(PAYMENT.AMOUNT.lt(new BigDecimal("1000")))
.where(PAYMENT.AMOUNT.le(new BigDecimal("1000")))

// BETWEEN
.where(PAYMENT.AMOUNT.between(new BigDecimal("100"), new BigDecimal("1000")))

// IN
.where(PAYMENT.STATUS.in("PAID", "REFUNDED"))

// LIKE
.where(PAYMENT.ORDER_NO.like("%2024%"))

// IS NULL / IS NOT NULL
.where(PAYMENT.REMARK.isNull())
.where(PAYMENT.REMARK.isNotNull())

// AND/OR
.where(PAYMENT.STATUS.eq("PAID"))
.and(PAYMENT.AMOUNT.ge(new BigDecimal("100")))
.or(PAYMENT.AMOUNT.ge(new BigDecimal("1000")))
```

### 6.4 JOIN查询

```java
public List<PaymentWithOrder> findPaymentsWithOrders() {
    QueryWrapper query = QueryWrapper.create()
            .select(
                PAYMENT.ALL_COLUMNS,
                ORDER.ORDER_NO,
                ORDER.USER_ID
            )
            .from(PAYMENT)
            .leftJoin(ORDER).on(PAYMENT.ORDER_ID.eq(ORDER.ID))
            .where(PAYMENT.IS_DELETED.eq(0));

    return paymentMapper.selectListByQuery(query);
}
```

---

## 7. 分页查询

### 7.1 基础分页

```java
@Service
@RequiredArgsConstructor
public class PaymentService {

    private final PaymentMapper paymentMapper;

    public Page<Payment> pageQuery(int pageNumber, int pageSize) {
        Page<Payment> page = new Page<>(pageNumber, pageSize);

        QueryWrapper query = QueryWrapper.create()
                .select()
                .from(PAYMENT)
                .where(PAYMENT.IS_DELETED.eq(0))
                .orderBy(PAYMENT.CREATE_TIME.desc());

        return paymentMapper.paginate(page, query);
    }
}
```

### 7.2 分页结果处理

```java
public PageResult<PaymentVO> pageQueryVO(int pageNumber, int pageSize) {
    Page<Payment> page = pageQuery(pageNumber, pageSize);

    // 转换为VO
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
```

---

## 8. 逻辑删除

### 8.1 配置逻辑删除

```java
@Data
@Table("payment")
public class Payment {

    @Id(keyType = KeyType.Auto)
    private Long id;

    private BigDecimal amount;

    // 逻辑删除字段：0-未删除，1-已删除
    @Column(isLogicDelete = true)
    private Integer isDeleted;
}
```

### 8.2 逻辑删除操作

```java
// 删除操作会自动设置is_deleted=1，而不是真正删除记录
paymentMapper.deleteById(paymentId);

// 查询操作会自动添加 is_deleted=0 条件
List<Payment> payments = paymentMapper.selectAll();

// 如果需要查询包括已删除的记录，使用ignoreLogicDelete
QueryWrapper query = QueryWrapper.create()
        .select()
        .from(PAYMENT)
        .ignoreLogicDelete();  // 忽略逻辑删除

List<Payment> allPayments = paymentMapper.selectListByQuery(query);
```

---

## 9. 乐观锁

### 9.1 配置乐观锁

```java
@Data
@Table("payment")
public class Payment {

    @Id(keyType = KeyType.Auto)
    private Long id;

    private BigDecimal amount;
    private PaymentStatusEnum status;

    // 乐观锁版本号字段
    @Column(version = true)
    private Integer version;
}
```

### 9.2 使用乐观锁

```java
@Service
@RequiredArgsConstructor
public class PaymentService {

    private final PaymentMapper paymentMapper;

    public void updatePaymentStatus(Long paymentId, PaymentStatusEnum newStatus) {
        // 1. 查询当前记录(包含version)
        Payment payment = paymentMapper.selectOneById(paymentId);

        // 2. 修改状态
        payment.setStatus(newStatus);

        // 3. 更新(MyBatis-Flex会自动在WHERE条件中加上version，并将version+1)
        int rows = paymentMapper.updateById(payment);

        if (rows == 0) {
            throw new BusinessException("更新失败，数据已被其他用户修改");
        }
    }
}
```

生成的SQL类似：
```sql
UPDATE payment
SET status = 'PAID', version = version + 1
WHERE id = 123 AND version = 5
```

---

## 10. 多数据源配置

### 10.1 配置文件

```yaml
spring:
  datasource:
    primary:
      url: jdbc:mysql://localhost:3306/db_primary
      username: root
      password: root
    secondary:
      url: jdbc:mysql://localhost:3306/db_secondary
      username: root
      password: root

mybatis-flex:
  datasource:
    primary:
      url: ${spring.datasource.primary.url}
      username: ${spring.datasource.primary.username}
      password: ${spring.datasource.primary.password}
    secondary:
      url: ${spring.datasource.secondary.url}
      username: ${spring.datasource.secondary.username}
      password: ${spring.datasource.secondary.password}
```

### 10.2 使用@DataSource注解

```java
@Service
public class PaymentService {

    @Autowired
    private PaymentMapper paymentMapper;

    // 使用主数据源(默认)
    public Payment findById(Long id) {
        return paymentMapper.selectOneById(id);
    }

    // 使用secondary数据源
    @DataSource("secondary")
    public Payment findByIdFromSecondary(Long id) {
        return paymentMapper.selectOneById(id);
    }
}
```

---

## 11. 代码生成器

### 11.1 生成器配置

```java
public class CodeGenerator {

    public static void main(String[] args) {
        // 数据源配置
        DataSource dataSource = new HikariDataSource();
        ((HikariDataSource) dataSource).setJdbcUrl("jdbc:mysql://localhost:3306/sbt_plus");
        ((HikariDataSource) dataSource).setUsername("root");
        ((HikariDataSource) dataSource).setPassword("root");

        // 全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setAuthor("kk");
        globalConfig.setOutputDir(System.getProperty("user.dir") + "/src/main/java");
        globalConfig.setEntityPackage("plus.sbt.payment.entity");
        globalConfig.setMapperPackage("plus.sbt.payment.mapper");
        globalConfig.setServicePackage("plus.sbt.payment.service");
        globalConfig.setServiceImplPackage("plus.sbt.payment.service.impl");
        globalConfig.setMapperXmlPath(System.getProperty("user.dir") + "/src/main/resources/mapper");

        // 策略配置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setGenerateEntity(true);
        strategyConfig.setGenerateMapper(true);
        strategyConfig.setGenerateService(true);
        strategyConfig.setGenerateServiceImpl(true);
        strategyConfig.setGenerateMapperXml(true);

        // 生成器
        Generator generator = new Generator(dataSource, globalConfig, strategyConfig);
        generator.generate();
    }
}
```

---

## 12. 最佳实践

### 12.1 Service层使用规范

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class PaymentServiceImpl implements PaymentService {

    private final PaymentMapper paymentMapper;

    @Override
    @Transactional(rollbackFor = Exception.class)
    public Payment createPayment(CreatePaymentRequest request) {
        // 1. 构建实体
        Payment payment = Payment.builder()
                .orderId(request.orderId())
                .amount(request.amount())
                .status(PaymentStatusEnum.WAITING_PAYMENT)
                .build();

        // 2. 插入数据库
        paymentMapper.insert(payment);

        // 3. 返回结果
        return payment;
    }

    @Override
    public Payment getById(Long id) {
        Payment payment = paymentMapper.selectOneById(id);
        if (payment == null) {
            throw new BusinessException("支付记录不存在");
        }
        return payment;
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void updateStatus(Long id, PaymentStatusEnum newStatus) {
        Payment payment = getById(id);
        payment.setStatus(newStatus);

        int rows = paymentMapper.updateById(payment);
        if (rows == 0) {
            throw new BusinessException("更新失败");
        }
    }
}
```

### 12.2 性能优化建议

1. **使用selectListByQuery而非selectAll**：避免查询全表数据
2. **合理使用索引**：确保WHERE条件中的字段有索引
3. **避免N+1查询问题**：使用JOIN或批量查询
4. **分页查询**：大数据量使用分页，避免一次性加载所有数据
5. **使用批量操作**：`insertBatch`、`deleteByIds` 等批量方法
6. **缓存热点数据**：结合Redis缓存频繁查询的数据

### 12.3 事务管理

```java
// 声明式事务
@Transactional(rollbackFor = Exception.class)
public void businessMethod() {
    // 业务逻辑
}

// 编程式事务
@Autowired
private TransactionTemplate transactionTemplate;

public void businessMethod() {
    transactionTemplate.execute(status -> {
        try {
            // 业务逻辑
            return result;
        } catch (Exception e) {
            status.setRollbackOnly();
            throw e;
        }
    });
}
```

### 12.4 异常处理

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class PaymentService {

    private final PaymentMapper paymentMapper;

    public Payment getById(Long id) {
        try {
            Payment payment = paymentMapper.selectOneById(id);
            if (payment == null) {
                throw new PaymentNotFoundException("支付记录不存在: " + id);
            }
            return payment;
        } catch (PaymentNotFoundException e) {
            log.error("支付记录不存在: {}", id);
            throw e;
        } catch (Exception e) {
            log.error("查询支付记录失败: {}", id, e);
            throw new BusinessException("查询失败", e);
        }
    }
}
```

---

## 参考资料

- [MyBatis-Flex官方文档](https://mybatis-flex.com/)
- [MyBatis-Flex GitHub](https://github.com/mybatis-flex/mybatis-flex)
- [MyBatis-Flex快速开始](https://mybatis-flex.com/zh/intro/getting-started.html)

---

**最后更新**: 2025-10-04
**维护者**: kk
