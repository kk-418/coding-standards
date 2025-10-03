# Java测试规范

> **作者**: kk
> **创建日期**: 2025-10-03
> **适用范围**: Java/Spring Boot项目测试

---

## 目录

1. [测试框架](#1-测试框架)
2. [测试依赖配置](#2-测试依赖配置)
3. [Mock注解使用规范](#3-mock注解使用规范)
4. [测试覆盖率要求](#4-测试覆盖率要求)
5. [测试命名规范](#5-测试命名规范)
6. [单元测试规范](#6-单元测试规范)
7. [集成测试规范](#7-集成测试规范)
8. [测试最佳实践](#8-测试最佳实践)

---

## 1. 测试框架

### 1.1 核心测试框架
- **单元测试**: JUnit 5 (Jupiter)
- **Mock框架**: Mockito
- **Spring测试**: Spring Boot Test
- **断言库**: AssertJ (推荐)、JUnit Assertions

### 1.2 推荐的测试工具
- **参数化测试**: JUnit 5 `@ParameterizedTest`
- **性能测试**: JMH (Java Microbenchmark Harness)
- **测试覆盖率**: JaCoCo
- **API测试**: REST Assured、MockMvc

---

## 2. 测试依赖配置

### 2.1 Gradle配置

```gradle
dependencies {
    // 核心测试依赖
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.junit.jupiter:junit-jupiter'
    testImplementation 'org.mockito:mockito-core'
    testImplementation 'org.mockito:mockito-junit-jupiter'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'

    // AssertJ断言库(推荐)
    testImplementation 'org.assertj:assertj-core:3.24.2'

    // REST API测试
    testImplementation 'io.rest-assured:rest-assured:5.3.0'
}

tasks.withType(Test).configureEach {
    useJUnitPlatform()
    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat "full"
        showStandardStreams = false
    }
}
```

### 2.2 Maven配置

```xml
<dependencies>
    <!-- 核心测试依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- AssertJ断言库 -->
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.24.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## 3. Mock注解使用规范

### 3.1 禁止使用过时的@MockBean

**重要**: `org.springframework.boot.test.mock.mockito.MockBean` 已被标记为过时且待删除,必须使用新的替代注解。

### 3.2 正确的Mock注解

| 场景 | 过时注解(禁止使用) | 正确注解(必须使用) |
|------|------------------|------------------|
| Mock Spring Bean | `@MockBean` | `@MockitoBean` |
| Spy Spring Bean | `@SpyBean` | `@MockitoSpyBean` |
| 纯Mockito Mock | - | `@Mock` |
| 纯Mockito Spy | - | `@Spy` |

### 3.3 错误示例

```java
// ❌ 错误: 使用过时的@MockBean
import org.springframework.boot.test.mock.mockito.MockBean;

@SpringBootTest
class PaymentServiceTest {

    @MockBean  // 过时注解,即将移除
    private PaymentRepository paymentRepository;

    @Test
    void testCreate() {
        // ...
    }
}
```

### 3.4 正确示例

```java
// ✅ 正确: 使用新的@MockitoBean
import org.springframework.boot.test.mock.mockito.MockitoBean;
import org.mockito.Mock;

@SpringBootTest
class PaymentServiceTest {

    @MockitoBean  // 新注解,替代@MockBean
    private PaymentRepository paymentRepository;

    @Autowired
    private PaymentService paymentService;

    @Test
    void should_createPayment_when_validRequest() {
        // Given
        Payment payment = Payment.builder()
                .id(1L)
                .amount(new BigDecimal("100.00"))
                .build();
        when(paymentRepository.save(any())).thenReturn(payment);

        // When
        PaymentResult result = paymentService.create(request);

        // Then
        assertNotNull(result);
        verify(paymentRepository).save(any());
    }
}

// ✅ 正确: 纯Mockito测试(不需要Spring上下文)
class PaymentCalculatorTest {

    @Mock  // 纯Mockito注解
    private RateService rateService;

    @InjectMocks
    private PaymentCalculator calculator;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void should_calculateFee_when_validAmount() {
        // Given
        when(rateService.getRate()).thenReturn(new BigDecimal("0.05"));

        // When
        BigDecimal fee = calculator.calculateFee(new BigDecimal("100"));

        // Then
        assertEquals(new BigDecimal("5.00"), fee);
    }
}
```

### 3.5 注解选择指南

**使用 `@MockitoBean` / `@MockitoSpyBean` 的场景**:
- 需要Spring上下文(使用 `@SpringBootTest` 或 `@WebMvcTest` 等)
- Mock Spring容器中的Bean
- 测试依赖Spring特性(如事务、AOP、依赖注入等)

**使用 `@Mock` / `@Spy` 的场景**:
- 纯单元测试,不需要Spring上下文
- 测试POJO类或工具类
- 追求更快的测试执行速度

### 3.6 迁移指南

如果项目中存在旧的 `@MockBean` 注解,按以下步骤迁移:

1. **查找所有使用**:
```bash
# 查找项目中所有使用@MockBean的文件
grep -r "@MockBean" src/test
```

2. **批量替换**:
```bash
# 替换import语句
find src/test -name "*.java" -exec sed -i '' \
  's/org\.springframework\.boot\.test\.mock\.mockito\.MockBean/org.springframework.boot.test.mock.mockito.MockitoBean/g' {} +

# 替换注解使用
find src/test -name "*.java" -exec sed -i '' \
  's/@MockBean/@MockitoBean/g' {} +
```

3. **验证编译**:
```bash
# 重新编译测试代码
gradle clean test
```

---

## 4. 测试覆盖率要求

### 4.1 覆盖率目标
- **单元测试覆盖率**: ≥ 80%
- **集成测试覆盖率**: ≥ 70%
- **API接口覆盖率**: 100%

### 4.2 JaCoCo配置

```gradle
plugins {
    id 'jacoco'
}

jacoco {
    toolVersion = "0.8.11"
}

jacocoTestReport {
    dependsOn test
    reports {
        xml.required = true
        html.required = true
    }
}

jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.80  // 80%覆盖率
            }
        }
    }
}

check.dependsOn jacocoTestCoverageVerification
```

---

## 5. 测试命名规范

### 5.1 测试类命名
- **单元测试**: `*Test.java`
- **集成测试**: `*IntegrationTest.java` 或 `*IT.java`
- **性能测试**: `*PerformanceTest.java`

### 5.2 测试方法命名

**推荐格式**:
- `should_<预期结果>_when_<测试条件>`
- `given_<前置条件>_when_<操作>_then_<预期结果>`

**示例**:
```java
@SpringBootTest
class PaymentServiceTest {

    @Test
    void should_createPayment_when_validRequest() {
        // Given
        CreatePaymentRequest request = new CreatePaymentRequest(
            1L,
            new BigDecimal("100.00"),
            "WECHAT"
        );

        // When
        PaymentResult result = paymentService.create(request);

        // Then
        assertNotNull(result);
        assertEquals("SUCCESS", result.getStatus());
    }

    @Test
    void should_throwException_when_amountIsNegative() {
        // Given
        CreatePaymentRequest request = new CreatePaymentRequest(
            1L,
            new BigDecimal("-100.00"),
            "WECHAT"
        );

        // When & Then
        assertThrows(IllegalArgumentException.class,
            () -> paymentService.create(request));
    }
}
```

---

## 6. 单元测试规范

### 6.1 测试结构 (Given-When-Then)

```java
@Test
void should_calculateDiscount_when_vipUser() {
    // Given - 准备测试数据
    User user = User.builder()
            .id(1L)
            .type(UserType.VIP)
            .build();
    BigDecimal amount = new BigDecimal("100.00");

    // When - 执行被测试方法
    BigDecimal discount = discountService.calculate(user, amount);

    // Then - 验证结果
    assertThat(discount).isEqualByComparingTo(new BigDecimal("90.00"));
}
```

### 6.2 使用AssertJ断言(推荐)

```java
import static org.assertj.core.api.Assertions.*;

@Test
void should_returnPaymentList_when_queryByStatus() {
    // Given
    PaymentStatus status = PaymentStatus.PAID;

    // When
    List<Payment> payments = paymentService.findByStatus(status);

    // Then
    assertThat(payments)
            .isNotEmpty()
            .hasSize(3)
            .allMatch(p -> p.getStatus() == status)
            .extracting(Payment::getAmount)
            .containsExactly(
                new BigDecimal("100.00"),
                new BigDecimal("200.00"),
                new BigDecimal("300.00")
            );
}
```

### 6.3 参数化测试

```java
@ParameterizedTest
@ValueSource(strings = {"WECHAT", "ALIPAY", "UNION_PAY"})
void should_supportPayment_when_validPaymentCompany(String company) {
    // When
    boolean supported = paymentService.isSupported(company);

    // Then
    assertThat(supported).isTrue();
}

@ParameterizedTest
@CsvSource({
    "100.00, VIP, 90.00",
    "100.00, NORMAL, 100.00",
    "100.00, WHOLESALE, 80.00"
})
void should_calculateCorrectDiscount_when_differentUserType(
        BigDecimal amount, UserType type, BigDecimal expected) {
    // Given
    User user = User.builder().type(type).build();

    // When
    BigDecimal result = discountService.calculate(user, amount);

    // Then
    assertThat(result).isEqualByComparingTo(expected);
}
```

### 6.4 异常测试

```java
@Test
void should_throwException_when_amountIsNull() {
    // Given
    CreatePaymentRequest request = new CreatePaymentRequest(1L, null, "WECHAT");

    // When & Then
    assertThatThrownBy(() -> paymentService.create(request))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("金额不能为空");
}
```

---

## 7. 集成测试规范

### 7.1 Spring Boot集成测试

```java
@SpringBootTest
@AutoConfigureMockMvc
class PaymentControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private PaymentRepository paymentRepository;

    @BeforeEach
    void setUp() {
        paymentRepository.deleteAll();
    }

    @Test
    void should_createPayment_when_validRequest() throws Exception {
        // Given
        String requestJson = """
            {
                "orderId": 1,
                "amount": "100.00",
                "paymentCompany": "WECHAT"
            }
            """;

        // When & Then
        mockMvc.perform(post("/api/payments")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestJson))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.status").value("SUCCESS"));
    }
}
```

### 7.2 数据库测试

```java
@DataJpaTest
class PaymentRepositoryTest {

    @Autowired
    private PaymentRepository paymentRepository;

    @Test
    void should_findPayments_when_queryByStatus() {
        // Given
        Payment payment1 = Payment.builder()
                .amount(new BigDecimal("100.00"))
                .status(PaymentStatus.PAID)
                .build();
        Payment payment2 = Payment.builder()
                .amount(new BigDecimal("200.00"))
                .status(PaymentStatus.PAID)
                .build();
        paymentRepository.saveAll(List.of(payment1, payment2));

        // When
        List<Payment> results = paymentRepository.findByStatus(PaymentStatus.PAID);

        // Then
        assertThat(results).hasSize(2);
    }
}
```

### 7.3 使用TestContainers测试

```java
@SpringBootTest
@Testcontainers
class PaymentServiceIntegrationTest {

    @Container
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")
            .withDatabaseName("testdb")
            .withUsername("test")
            .withPassword("test");

    @DynamicPropertySource
    static void properties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mysql::getJdbcUrl);
        registry.add("spring.datasource.username", mysql::getUsername);
        registry.add("spring.datasource.password", mysql::getPassword);
    }

    @Autowired
    private PaymentService paymentService;

    @Test
    void should_persistPayment_when_create() {
        // Given
        CreatePaymentRequest request = new CreatePaymentRequest(
            1L,
            new BigDecimal("100.00"),
            "WECHAT"
        );

        // When
        PaymentResult result = paymentService.create(request);

        // Then
        assertThat(result.getStatus()).isEqualTo("SUCCESS");
    }
}
```

---

## 8. 测试最佳实践

### 8.1 测试独立性

**原则**: 每个测试应该独立运行,不依赖其他测试的执行顺序

```java
// ✅ 正确: 每个测试独立准备数据
@BeforeEach
void setUp() {
    paymentRepository.deleteAll();
}

@Test
void test1() {
    Payment payment = createTestPayment();
    // ...
}

@Test
void test2() {
    Payment payment = createTestPayment();
    // ...
}

// ❌ 错误: test2依赖test1的执行结果
@Test
void test1() {
    paymentRepository.save(payment);
}

@Test
void test2() {
    List<Payment> payments = paymentRepository.findAll();
    assertThat(payments).hasSize(1);  // 依赖test1
}
```

### 8.2 避免过度Mock

**原则**: 只Mock外部依赖,内部逻辑使用真实对象

```java
// ✅ 正确: 只Mock外部依赖
@SpringBootTest
class OrderServiceTest {

    @MockitoBean
    private PaymentClient paymentClient;  // 外部服务

    @Autowired
    private OrderService orderService;  // 被测试对象,使用真实实例

    @Test
    void should_createOrder_when_paymentSuccess() {
        // Mock外部服务
        when(paymentClient.pay(any())).thenReturn(PaymentResult.success());

        // 测试真实的业务逻辑
        Order order = orderService.create(request);

        assertThat(order.getStatus()).isEqualTo(OrderStatus.PAID);
    }
}
```

### 8.3 测试边界条件

```java
@Test
void should_handleEdgeCases() {
    // 测试空值
    assertThatThrownBy(() -> service.process(null))
            .isInstanceOf(IllegalArgumentException.class);

    // 测试空集合
    List<String> emptyList = Collections.emptyList();
    assertThat(service.process(emptyList)).isEmpty();

    // 测试边界值
    assertThat(service.calculate(0)).isEqualTo(0);
    assertThat(service.calculate(Integer.MAX_VALUE)).isNotNull();
}
```

### 8.4 使用测试数据构建器

```java
// 测试数据构建器
class PaymentTestBuilder {
    private Long id = 1L;
    private BigDecimal amount = new BigDecimal("100.00");
    private PaymentStatus status = PaymentStatus.WAITING_PAYMENT;

    public PaymentTestBuilder withId(Long id) {
        this.id = id;
        return this;
    }

    public PaymentTestBuilder withAmount(BigDecimal amount) {
        this.amount = amount;
        return this;
    }

    public PaymentTestBuilder withStatus(PaymentStatus status) {
        this.status = status;
        return this;
    }

    public Payment build() {
        return Payment.builder()
                .id(id)
                .amount(amount)
                .status(status)
                .build();
    }
}

// 使用构建器
@Test
void test() {
    Payment payment = new PaymentTestBuilder()
            .withAmount(new BigDecimal("200.00"))
            .withStatus(PaymentStatus.PAID)
            .build();
    // ...
}
```

### 8.5 测试性能基准

```java
@Test
@Timeout(value = 100, unit = TimeUnit.MILLISECONDS)
void should_completeWithin100ms_when_processPayment() {
    // 测试方法必须在100ms内完成
    paymentService.process(request);
}
```

### 8.6 清理测试资源

```java
@AfterEach
void tearDown() {
    // 清理测试数据
    paymentRepository.deleteAll();

    // 重置Mock
    Mockito.reset(externalService);
}

@AfterAll
static void cleanup() {
    // 清理静态资源
}
```

---

## 9. 测试反模式(避免)

### 9.1 不要测试框架本身

```java
// ❌ 错误: 测试Spring的依赖注入
@Test
void should_autowireBean() {
    assertNotNull(paymentService);  // 无意义
}

// ✅ 正确: 测试业务逻辑
@Test
void should_createPayment_when_validRequest() {
    PaymentResult result = paymentService.create(request);
    assertThat(result.getStatus()).isEqualTo("SUCCESS");
}
```

### 9.2 不要在测试中使用逻辑判断

```java
// ❌ 错误: 测试中包含if逻辑
@Test
void test() {
    Payment payment = paymentService.getById(1L);
    if (payment != null) {
        assertEquals(PaymentStatus.PAID, payment.getStatus());
    }
}

// ✅ 正确: 明确的断言
@Test
void should_returnPaidPayment_when_queryById() {
    Payment payment = paymentService.getById(1L);
    assertThat(payment).isNotNull();
    assertThat(payment.getStatus()).isEqualTo(PaymentStatus.PAID);
}
```

### 9.3 不要使用Thread.sleep

```java
// ❌ 错误: 使用sleep等待异步结果
@Test
void test() throws InterruptedException {
    asyncService.process();
    Thread.sleep(1000);  // 不可靠
    verify(repository).save(any());
}

// ✅ 正确: 使用Awaitility等待
@Test
void should_processAsync() {
    asyncService.process();

    await().atMost(Duration.ofSeconds(5))
            .untilAsserted(() -> verify(repository).save(any()));
}
```

---

## 10. 常见问题排查

### 10.1 Mock不生效

**问题**: Mock的方法没有返回预期值

**解决**:
1. 检查Mock参数匹配: 使用 `any()` 或精确匹配
2. 确认Mock在方法调用之前配置
3. 验证是否Mock了正确的Bean

```java
// 使用ArgumentCaptor捕获实际参数
ArgumentCaptor<Payment> captor = ArgumentCaptor.forClass(Payment.class);
verify(repository).save(captor.capture());
System.out.println("实际参数: " + captor.getValue());
```

### 10.2 测试数据污染

**问题**: 测试相互影响

**解决**:
```java
@BeforeEach
void setUp() {
    // 每个测试前清理数据
    repository.deleteAll();
}

// 或使用@DirtiesContext(慎用,影响性能)
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)
```

### 10.3 Spring上下文加载慢

**问题**: 集成测试启动慢

**解决**:
1. 使用 `@WebMvcTest`、`@DataJpaTest` 等切片测试
2. 复用Spring上下文(避免频繁使用@DirtiesContext)
3. 优先使用纯Mockito单元测试

---

**最后更新**: 2025-10-03
**维护者**: kk
