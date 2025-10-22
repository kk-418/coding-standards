# Java 21 使用规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: Java 21项目开发

---

## 目录
1. [Java 21简介](#java-21简介)
2. [版本要求](#版本要求)
3. [推荐使用的Java 21特性](#推荐使用的java-21特性)
4. [Virtual Threads(虚拟线程)](#virtual-threads虚拟线程)
5. [Record Classes(记录类)](#record-classes记录类)
6. [Pattern Matching(模式匹配)](#pattern-matching模式匹配)
7. [Sealed Classes(密封类)](#sealed-classes密封类)
8. [Text Blocks(文本块)](#text-blocks文本块)
9. [Switch表达式](#switch表达式)
10. [其他新特性](#其他新特性)
11. [最佳实践](#最佳实践)

---

## 1. Java 21简介

Java 21是一个**长期支持(LTS)**版本，发布于2023年9月，提供以下重要特性：
- **Virtual Threads (JEP 444)**: 轻量级线程，简化并发编程
- **Sequenced Collections (JEP 431)**: 有序集合API
- **Record Patterns (JEP 440)**: 记录模式匹配
- **Pattern Matching for switch (JEP 441)**: switch模式匹配
- **String Templates (Preview)**: 字符串模板(预览)
- **Unnamed Classes and Instance Main Methods (Preview)**: 简化main方法

---

## 2. 版本要求

### 2.1 项目要求
- **最低版本**: Java 21
- **源代码兼容性**: Java 21
- **目标字节码**: Java 21

### 2.2 编译器配置

**Gradle配置**:
```gradle
java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21
}

tasks.withType(JavaCompile).configureEach {
    options.encoding = 'UTF-8'
    options.compilerArgs += ['-parameters']  // 保留参数名
}

compileJava {
    options.release.set(21)  // 使用release标志替代source/target
}
```

**Maven配置**:
```xml
<properties>
    <java.version>21</java.version>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <maven.compiler.release>21</maven.compiler.release>
</properties>
```

---

## 3. 推荐使用的Java 21特性

优先使用以下现代Java特性提升代码质量：

| 特性 | 用途 | 优先级 |
|------|------|--------|
| Virtual Threads | 异步服务、高并发场景 | ⭐⭐⭐ |
| Record Classes | DTO/VO/Request/Response | ⭐⭐⭐ |
| Pattern Matching | 复杂业务逻辑判断 | ⭐⭐⭐ |
| Switch表达式 | 多分支逻辑 | ⭐⭐⭐ |
| Sealed Classes | 限定继承层次 | ⭐⭐ |
| Text Blocks | 多行字符串(SQL/JSON) | ⭐⭐ |

---

## 4. Virtual Threads(虚拟线程)

### 4.1 什么是Virtual Threads

虚拟线程是轻量级线程,由JVM管理,而非操作系统管理。相比传统平台线程:
- **轻量**: 创建数百万个虚拟线程不会耗尽内存
- **简单**: 使用传统的同步代码风格,无需回调
- **高效**: 适合I/O密集型任务

### 4.2 Spring Boot中使用Virtual Threads

**配置虚拟线程Executor**:
```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "virtualThreadExecutor")
    public Executor virtualThreadExecutor() {
        return Executors.newVirtualThreadPerTaskExecutor();
    }

    @Bean(name = "platformThreadExecutor")
    public Executor platformThreadExecutor() {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
                10, 50, 60L, TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(100),
                new ThreadFactoryBuilder().setNameFormat("platform-thread-%d").build(),
                new ThreadPoolExecutor.CallerRunsPolicy()
        );
        return executor;
    }
}
```

**使用虚拟线程执行异步任务**:
```java
@Service
@Slf4j
public class PaymentService {

    @Async("virtualThreadExecutor")
    public CompletableFuture<PaymentResult> processPaymentAsync(PaymentRequest request) {
        log.info("Processing payment in virtual thread: {}", Thread.currentThread());

        // I/O密集型操作(如调用第三方支付API)
        PaymentResult result = callPaymentGateway(request);

        return CompletableFuture.completedFuture(result);
    }
}
```

### 4.3 手动创建Virtual Threads

```java
// 创建单个虚拟线程
Thread virtualThread = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread: " + Thread.currentThread());
});
virtualThread.join();

// 使用ExecutorService
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    List<Future<String>> futures = new ArrayList<>();

    for (int i = 0; i < 10000; i++) {
        int taskId = i;
        Future<String> future = executor.submit(() -> {
            // I/O操作
            Thread.sleep(1000);
            return "Task " + taskId + " completed";
        });
        futures.add(future);
    }

    for (Future<String> future : futures) {
        System.out.println(future.get());
    }
}
```

### 4.4 Virtual Threads最佳实践

**适用场景**:
- ✅ I/O密集型任务(HTTP请求、数据库查询、文件读写)
- ✅ 高并发场景(需要处理大量并发请求)
- ✅ 简化异步代码(避免回调地狱)

**不适用场景**:
- ❌ CPU密集型任务(计算密集,使用传统线程池更好)
- ❌ 需要线程池复用的场景
- ❌ 需要精确控制线程数量的场景

**注意事项**:
- 避免在虚拟线程中使用 `synchronized`,可能导致平台线程被固定(pinned)
- 优先使用 `ReentrantLock` 而非 `synchronized`
- 不要限制虚拟线程的数量,让JVM自动管理

---

## 5. Record Classes(记录类)

### 5.1 什么是Record

Record是不可变数据类,自动生成构造器、getter、equals、hashCode、toString方法。

### 5.2 定义Record

```java
// 简单Record
public record PaymentVO(
    String id,
    String amount,
    String status,
    String createTime
) {}

// 带验证的Record
public record CreatePaymentRequest(
    Long orderId,
    BigDecimal amount,
    String paymentCompany
) {
    // 紧凑构造器 - 用于验证
    public CreatePaymentRequest {
        if (orderId == null || orderId <= 0) {
            throw new IllegalArgumentException("orderId必须大于0");
        }
        if (amount == null || amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("amount必须大于0");
        }
    }
}

// 带自定义方法的Record
public record PaymentRecord(
    Long id,
    BigDecimal amount,
    PaymentStatusEnum status
) {
    // 自定义方法
    public boolean isPaid() {
        return status == PaymentStatusEnum.PAID;
    }

    public String getFormattedAmount() {
        return amount.setScale(2, RoundingMode.HALF_UP).toString();
    }
}
```

### 5.3 Record使用场景

- **DTO/VO**: 前后端数据传输对象
- **Request/Response**: API请求响应对象
- **配置类**: 不可变配置信息
- **事件对象**: 领域事件

**示例**:
```java
// VO
public record UserVO(
    String id,
    String username,
    String email,
    String createTime
) {}

// Request
public record LoginRequest(
    String username,
    String password
) {}

// Response
public record ApiResponse<T>(
    int code,
    String message,
    T data
) {
    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<>(200, "成功", data);
    }

    public static <T> ApiResponse<T> error(String message) {
        return new ApiResponse<>(500, message, null);
    }
}
```

---

## 6. Pattern Matching(模式匹配)

### 6.1 instanceof模式匹配

**旧方式**:
```java
Object obj = getObject();
if (obj instanceof String) {
    String s = (String) obj;  // 需要强制转换
    System.out.println(s.toLowerCase());
}
```

**Java 21方式**:
```java
Object obj = getObject();
if (obj instanceof String s) {  // 直接声明变量s
    System.out.println(s.toLowerCase());  // 无需强制转换
}
```

### 6.2 Record Pattern(记录模式)

```java
public record Point(int x, int y) {}

// 旧方式
if (obj instanceof Point) {
    Point p = (Point) obj;
    int x = p.x();
    int y = p.y();
    System.out.println("Point: " + x + ", " + y);
}

// Java 21方式 - Record Pattern
if (obj instanceof Point(int x, int y)) {
    System.out.println("Point: " + x + ", " + y);
}
```

### 6.3 嵌套模式匹配

```java
public record Order(Long id, Payment payment) {}
public record Payment(BigDecimal amount, PaymentStatusEnum status) {}

// 嵌套解构
if (obj instanceof Order(Long id, Payment(BigDecimal amount, var status))) {
    System.out.println("Order " + id + " amount: " + amount + " status: " + status);
}
```

---

## 7. Sealed Classes(密封类)

### 7.1 什么是Sealed Classes

密封类限制了哪些类可以继承或实现它,提供更好的类型安全。

### 7.2 定义Sealed Classes

```java
// 密封接口
public sealed interface PaymentMethod
        permits WechatPayment, AlipayPayment, UnionPayment {

    PaymentResult pay(BigDecimal amount);
}

// 允许的实现类
public final class WechatPayment implements PaymentMethod {
    @Override
    public PaymentResult pay(BigDecimal amount) {
        // 微信支付逻辑
        return new PaymentResult("SUCCESS", "微信支付成功");
    }
}

public final class AlipayPayment implements PaymentMethod {
    @Override
    public PaymentResult pay(BigDecimal amount) {
        // 支付宝逻辑
        return new PaymentResult("SUCCESS", "支付宝支付成功");
    }
}

public final class UnionPayment implements PaymentMethod {
    @Override
    public PaymentResult pay(BigDecimal amount) {
        // 银联支付逻辑
        return new PaymentResult("SUCCESS", "银联支付成功");
    }
}
```

### 7.3 Sealed Classes + Pattern Matching

```java
public PaymentResult processPayment(PaymentMethod method, BigDecimal amount) {
    return switch (method) {
        case WechatPayment wp -> wp.pay(amount);
        case AlipayPayment ap -> ap.pay(amount);
        case UnionPayment up -> up.pay(amount);
        // 编译器确保覆盖所有情况,无需default分支
    };
}
```

---

## 8. Text Blocks(文本块)

### 8.1 基本用法

**旧方式**:
```java
String sql = "SELECT id, user_name, email\n" +
             "FROM user\n" +
             "WHERE status = 'ACTIVE'\n" +
             "ORDER BY create_time DESC";
```

**Java 21方式**:
```java
String sql = """
        SELECT id, user_name, email
        FROM user
        WHERE status = 'ACTIVE'
        ORDER BY create_time DESC
        """;
```

### 8.2 使用场景

**SQL查询**:
```java
String complexQuery = """
        SELECT p.id, p.amount, p.status,
               o.order_no, u.username
        FROM payment p
        INNER JOIN order o ON p.order_id = o.id
        INNER JOIN user u ON o.user_id = u.id
        WHERE p.status = 'PAID'
          AND p.create_time >= ?
          AND p.create_time < ?
        ORDER BY p.create_time DESC
        LIMIT ?, ?
        """;
```

**JSON字符串**:
```java
String jsonTemplate = """
        {
          "orderId": "%s",
          "amount": "%s",
          "paymentCompany": "%s",
          "callbackUrl": "https://example.com/callback"
        }
        """.formatted(orderId, amount, paymentCompany);
```

**HTML模板**:
```java
String html = """
        <!DOCTYPE html>
        <html>
        <head>
            <title>支付结果</title>
        </head>
        <body>
            <h1>支付%s</h1>
            <p>订单号: %s</p>
            <p>金额: %s</p>
        </body>
        </html>
        """.formatted(status, orderNo, amount);
```

---

## 9. Switch表达式

### 9.1 基本Switch表达式

**旧方式**:
```java
String paymentName;
switch (paymentType) {
    case "WECHAT":
        paymentName = "微信支付";
        break;
    case "ALIPAY":
        paymentName = "支付宝";
        break;
    case "UNION":
        paymentName = "银联支付";
        break;
    default:
        throw new IllegalArgumentException("未知支付类型");
}
```

**Java 21方式**:
```java
String paymentName = switch (paymentType) {
    case "WECHAT" -> "微信支付";
    case "ALIPAY" -> "支付宝";
    case "UNION" -> "银联支付";
    default -> throw new IllegalArgumentException("未知支付类型");
};
```

### 9.2 Switch Pattern Matching

```java
public String describe(Object obj) {
    return switch (obj) {
        case null -> "null值";
        case String s -> "字符串: " + s;
        case Integer i -> "整数: " + i;
        case Long l when l > 1000 -> "大整数: " + l;  // Guard条件
        case Long l -> "整数: " + l;
        case List<?> list -> "列表,大小: " + list.size();
        default -> "未知类型: " + obj.getClass().getName();
    };
}
```

### 9.3 Switch + Record Pattern

```java
public BigDecimal calculateFee(Payment payment) {
    return switch (payment) {
        case Payment(var amount, PaymentStatusEnum.PAID) ->
            amount.multiply(new BigDecimal("0.006"));  // 已支付收取0.6%手续费
        case Payment(var amount, PaymentStatusEnum.REFUNDED) ->
            BigDecimal.ZERO;  // 已退款无手续费
        case Payment(_, _) ->
            BigDecimal.ZERO;  // 其他状态无手续费
    };
}
```

---

## 10. 其他新特性

### 10.1 Sequenced Collections

Java 21引入有序集合API,提供统一的访问方式：

```java
// List, Deque, SortedSet都实现了SequencedCollection接口
List<String> list = List.of("a", "b", "c");

// 获取第一个和最后一个元素
String first = list.getFirst();   // "a"
String last = list.getLast();     // "c"

// 反转集合
List<String> reversed = list.reversed();  // ["c", "b", "a"]
```

### 10.2 String Templates (Preview)

```java
// 注意: 需要启用preview特性
String name = "张三";
int age = 25;

// 字符串模板
String message = STR."用户\{name}的年龄是\{age}岁";
// 输出: 用户张三的年龄是25岁
```

---

## 11. 最佳实践

### 11.1 优先使用新特性

```java
// ✅ 推荐: 使用Record
public record UserDTO(String id, String name, String email) {}

// ❌ 不推荐: 传统类+Lombok
@Data
@AllArgsConstructor
public class UserDTO {
    private String id;
    private String name;
    private String email;
}
```

### 11.2 合理使用Virtual Threads

```java
// ✅ I/O密集型场景
@Async("virtualThreadExecutor")
public CompletableFuture<ApiResponse> callExternalApi(String url) {
    // HTTP请求
    return CompletableFuture.completedFuture(httpClient.get(url));
}

// ❌ CPU密集型场景(应使用传统线程池)
@Async("platformThreadExecutor")
public CompletableFuture<BigInteger> calculatePrime(int n) {
    // 计算密集
    return CompletableFuture.completedFuture(calculatePrimeNumber(n));
}
```

### 11.3 模式匹配简化代码

```java
// ✅ 推荐: Pattern Matching
if (obj instanceof String s && s.length() > 10) {
    System.out.println("长字符串: " + s);
}

// ❌ 不推荐: 传统方式
if (obj instanceof String) {
    String s = (String) obj;
    if (s.length() > 10) {
        System.out.println("长字符串: " + s);
    }
}
```

### 11.4 Switch表达式提高可读性

```java
// ✅ 推荐: Switch表达式
return switch (orderStatus) {
    case CREATED -> "订单已创建";
    case PAID -> "订单已支付";
    case SHIPPED -> "订单已发货";
    case COMPLETED -> "订单已完成";
    case CANCELLED -> "订单已取消";
};

// ❌ 不推荐: 传统switch
String message;
switch (orderStatus) {
    case CREATED:
        message = "订单已创建";
        break;
    case PAID:
        message = "订单已支付";
        break;
    // ...
    default:
        message = "未知状态";
}
return message;
```

---

## 参考资料

- [Java 21官方文档](https://docs.oracle.com/en/java/javase/21/)
- [OpenJDK JEP列表](https://openjdk.org/jeps/0)
- [Java 21新特性概览](https://www.oracle.com/java/technologies/javase/21-relnote-issues.html)
- [Virtual Threads详解](https://openjdk.org/jeps/444)

---

**最后更新**: 2025-10-01
**维护者**: kk
