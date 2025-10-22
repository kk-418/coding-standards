# 日志使用规范检查

检查代码中的日志使用是否符合规范。

## 检查规则

### ✅ 正确的日志使用
- 使用 `@Slf4j` 注解 (Lombok)
- 使用 SLF4J API: `log.debug()`, `log.info()`, `log.warn()`, `log.error()`
- 使用占位符: `log.info("User {} logged in", userId)`
- 合适的日志级别:
  - `DEBUG`: 详细调试信息
  - `INFO`: 关键业务流程信息
  - `WARN`: 警告信息(可恢复的异常)
  - `ERROR`: 错误信息(需要处理的异常)

### ❌ 禁止的日志使用
- `System.out.println()` - 生产代码中严格禁止
- `System.err.println()` - 生产代码中严格禁止
- `e.printStackTrace()` - 应使用 `log.error("message", e)`
- 字符串拼接: `log.info("User " + userId + " logged in")` - 应使用占位符

## 检查步骤

1. 扫描所有 Java 源文件
2. 查找 `System.out.println` 和 `System.err.println` 的使用
3. 查找 `e.printStackTrace()` 的使用
4. 检查是否使用 `@Slf4j` 注解
5. 检查日志格式是否使用占位符
6. 报告违规情况并给出修复建议

## 示例

### ❌ 错误示例
```java
public class UserService {
    public void login(String userId) {
        System.out.println("User logged in: " + userId);  // 错误

        try {
            // ...
        } catch (Exception e) {
            e.printStackTrace();  // 错误
            System.err.println("Login failed");  // 错误
        }
    }
}
```

### ✅ 正确示例
```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class UserService {
    public void login(String userId) {
        log.info("User logged in: {}", userId);  // 正确

        try {
            // ...
        } catch (Exception e) {
            log.error("Login failed for user: {}", userId, e);  // 正确
        }
    }
}
```

## 日志级别选择指南

- **DEBUG**: `log.debug("Processing request with params: {}", params)`
- **INFO**: `log.info("User {} successfully logged in", userId)`
- **WARN**: `log.warn("Retry attempt {} failed, will retry", attemptCount)`
- **ERROR**: `log.error("Failed to process payment for order {}", orderId, exception)`

## 执行检查

请扫描当前项目的 Java 源文件,并报告所有不符合日志使用规范的代码。
