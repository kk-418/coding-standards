# 编码规范

## 代码质量基本要求

- **编译和启动保证**: 代码修改完成后必须保证可编译、可启动，不能提交无法运行的代码
- **引用规范**: 不得在方法里面使用全限定类名，应该使用import的方式引入类
- **Git版本管理规范**: 在有git版本管理的项目下，所有文件移动或重命名操作必须使用 `git mv` 命令，而不是直接使用 `mv` 命令，以保持版本历史的连续性

## 集合处理规范

- **集合判空**: 判断集合是否为空应该使用Apache Commons库的 `CollectionUtils.isEmpty()` 和 `CollectionUtils.isNotEmpty()` 方法，而不是直接使用 `collection.size() == 0` 或 `collection.isEmpty()`
  ```java
  // 推荐
  if (CollectionUtils.isEmpty(list)) {
      // 处理空集合
  }

  // 不推荐
  if (list == null || list.size() == 0) {
      // 处理空集合
  }
  ```
- **集合转数组**: 使用集合的 `toArray(T[] array)` 方法时，传入的数组类型必须与集合元素类型一致，且数组大小为 `collection.size()`
- **集合遍历**: 需要同时获取key和value时，使用 `entrySet()` 而不是 `keySet()` 遍历

## 命名规范

- **类名**: 使用大驼峰命名法(UpperCamelCase)，例如 `UserService`、`OrderController`
- **方法名、参数名、成员变量、局部变量**: 使用小驼峰命名法(lowerCamelCase)，例如 `getUserName()`、`userId`
- **常量**: 全部大写，单词间用下划线分隔，例如 `MAX_STOCK_COUNT`
- **包名**: 统一使用小写，点分隔符之间有且仅有一个自然语义的英文单词，例如 `com.alibaba.ai.container`
- **抽象类**: 以 `Abstract` 或 `Base` 开头，例如 `AbstractUserService`、`BaseController`
- **异常类**: 以 `Exception` 结尾，例如 `UserNotFoundException`
- **测试类**: 以被测试类名开始，以 `Test` 结尾，例如 `UserServiceTest`

## 常量定义规范

- **魔法值**: 禁止在代码中出现任何魔法值(即未经定义的常量)，必须定义为常量并使用常量名
  ```java
  // 推荐
  public static final String USER_CACHE_KEY = "user:cache:";
  String cacheKey = USER_CACHE_KEY + userId;

  // 不推荐
  String cacheKey = "user:cache:" + userId;
  ```
- **Long常量后缀**: long或Long类型的常量值后必须使用大写字母L，不能使用小写字母l，以避免与数字1混淆
- **常量复用**: 不要使用一个常量类维护所有常量，应该按功能模块划分常量类，例如"缓存相关常量"放在 `CacheConstants` 类中

## 日期时间处理规范

- **日期格式**: 统一使用 `yyyy-MM-dd` 格式表示日期，使用 `yyyy-MM-dd HH:mm:ss` 格式表示日期时间
- **避免使用旧API**: 不要使用 `java.sql.Date`、`java.sql.Time`、`java.sql.Timestamp`，应该使用 `java.time` 包下的 `LocalDate`、`LocalTime`、`LocalDateTime` 等类
- **时区处理**: 涉及时区转换时，必须使用 `ZonedDateTime` 而不是 `Date`

## 并发处理规范

- **线程池创建**: 必须通过 `ThreadPoolExecutor` 的方式创建线程池，不允许使用 `Executors` 去创建，以便更加明确线程池的运行规则，规避资源耗尽的风险
  ```java
  // 推荐
  ThreadPoolExecutor executor = new ThreadPoolExecutor(
      5, 10, 60L, TimeUnit.SECONDS,
      new LinkedBlockingQueue<>(100),
      new ThreadFactoryBuilder().setNameFormat("XX-task-%d").build(),
      new ThreadPoolExecutor.AbortPolicy()
  );

  // 不推荐
  ExecutorService executor = Executors.newFixedThreadPool(10);
  ```
- **ThreadLocal使用**: 必须回收自定义的 `ThreadLocal` 变量，尤其在线程池场景下，线程经常会被复用，如果不清理自定义的 `ThreadLocal` 变量，可能会影响后续业务逻辑和造成内存泄露等问题
  ```java
  try {
      threadLocal.set(value);
      // 业务逻辑
  } finally {
      threadLocal.remove();
  }
  ```

## 异常处理规范

- **异常捕获**: 不要捕获 `Throwable` 和 `Error`，应该捕获具体的异常类型
- **异常处理**: 捕获异常后必须进行处理，不能简单地使用 `e.printStackTrace()` 或者直接忽略
- **事务回滚**: 在事务场景下，抛出异常应该使用运行时异常(RuntimeException)以确保事务回滚
- **finally块**: 不要在 `finally` 块中使用 `return`，因为finally块的返回值会覆盖try/catch块的返回值
