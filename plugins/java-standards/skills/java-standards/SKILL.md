---
name: java-standards
description: Java/Spring Boot 编码规范,包含Java 21特性、VO类型约束、日志使用、测试规范等。当开发Java项目、创建Entity/VO/Service类、使用Spring Boot、编写Java测试时使用此Skill。
---

# Java 编码规范

本 Skill 提供 Java 21 + Spring Boot 项目的完整编码规范。

## 核心规范

### 1. Java 编码标准 (java-coding-standards.md)

**强制性规范:**
- **VO 类型约束**(编译时检查):
  - ✅ 所有字段使用 `String` 类型
  - ❌ 禁止 `BigDecimal`, `Long`(ID字段), `LocalDateTime`, `LocalDate`
- **日志使用**:
  - ✅ 使用 `@Slf4j` 注解 + SLF4J
  - ❌ 禁止 `System.out.println()` 和 `System.err.println()`
- **Result 使用**:
  - ✅ 分页查询直接返回 `PageResult<T>`
  - ❌ 禁止 `Result<PageResult<T>>` 嵌套
- **命名规范**:
  - 枚举类以 `Enum` 结尾
  - VO 类以 `VO` 结尾
  - Service 实现以 `ServiceImpl` 结尾

### 2. Java 21 特性 (java21-standards.md)
- Virtual Threads (虚拟线程) - 高并发 I/O 场景
- Record Classes (记录类) - DTO/VO 定义
- Pattern Matching (模式匹配) - 类型判断简化
- Switch 表达式 - 多分支逻辑

### 3. 测试规范 (java-test-standards.md)
- **Mock 注解**: 使用 `@MockitoBean`,禁止过时的 `@MockBean`
- **覆盖率要求**: 单元测试 ≥ 80%, 集成测试 ≥ 70%
- **命名规范**: `should_<预期结果>_when_<测试条件>`
- **断言工具**: 推荐使用 AssertJ

## 使用场景

- 创建 Java 类(Entity/VO/DTO/Service等)
- 使用 Lombok 或 Record 类
- 编写 Spring Boot 应用
- 编写单元测试和集成测试
- 代码审查和质量检查

## 详细文档

请参考同目录下的:
- `java-coding-standards.md` - 完整 Java 编码规范
- `java21-standards.md` - Java 21 新特性使用指南
- `java-test-standards.md` - 测试编写和最佳实践

---

**作者**: kk
**版本**: 1.0.0
**最后更新**: 2025-10-22
