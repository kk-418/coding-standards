# Java 编码规范

快速加载 Java/Spring Boot 编码规范,包含 Java 21 特性和测试规范。

请参考以下规范文档,并在生成代码时严格遵守:

## 核心规范

### 1. VO 类型约束(强制)
- ✅ 所有字段使用 `String` 类型
- ❌ 禁止使用 `BigDecimal`, `Long`(ID字段), `LocalDateTime`, `LocalDate`

### 2. 日志使用规范(强制)
- ✅ 使用 `@Slf4j` 注解 + SLF4J
- ❌ 禁止使用 `System.out.println()` 和 `System.err.println()`

### 3. Result 使用规范(强制)
- ✅ 分页查询直接返回 `PageResult<T>`
- ❌ 禁止使用 `Result<PageResult<T>>` 嵌套

### 4. 命名规范(强制)
- 枚举类以 `Enum` 结尾(如 `PaymentStatusEnum`)
- VO 类以 `VO` 结尾(如 `UserVO`)
- Service 实现以 `ServiceImpl` 结尾

### 5. 测试规范(强制)
- Mock 注解: 使用 `@MockitoBean`,禁止 `@MockBean`
- 测试命名: `should_<预期结果>_when_<测试条件>`
- 覆盖率要求: 单元测试 ≥ 80%

## 详细文档

完整规范请参考 `skills/java-standards/` 目录下的文档:
- `java-coding-standards.md` - Java 编码规范详细说明
- `java21-standards.md` - Java 21 新特性使用指南
- `java-test-standards.md` - 测试编写最佳实践
