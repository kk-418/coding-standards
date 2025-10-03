# 编码规范索引

> **作者**: kk
> **创建日期**: 2025-10-01
> **用途**: 为Claude提供编码规范文档索引，快速定位相关规范

---

## 📋 工作指南

### [guidelines.md](./guidelines.md) ⭐⭐⭐
**工作规范**: AI辅助开发的基本工作规范和流程要求

**核心规范**:
- **作者信息规则**: 统一使用"kk"作为作者
- **输出语言规则**: 使用简体中文(技术术语除外)
- **正面解决问题**: 直接解决根本原因,不绕过
- **临时文件管理**: 临时文件放在项目temp目录
- **任务跟踪规则**: 在temp目录生成todo.md跟踪任务进度 ⭐

---

## 🐛 调试规范

### [debug.md](./debug.md) ⭐⭐⭐
**调试方法**: 基于证据的调试方法和流程,禁止猜测式调试

**核心原则**:
- **禁止猜测**: 不允许凭感觉猜测问题原因
- **基于证据**: 所有结论必须有证据支撑
- **日志验证**: 证据不足时必须通过日志进一步定位
- **系统性分析**: 逐步缩小问题范围,找到根本原因

**章节目录**:
1. 核心原则(禁止猜测式调试)
2. 调试流程(收集证据→分析→验证→定位)
3. 日志添加规范(关键位置、日志级别、日志内容)
4. 调试工具(日志工具、调试配置、IDE调试)
5. 调试案例(正确vs错误的调试方式)
6. 调试检查清单

---

## 🌟 代码质量价值观

### [values.md](./values.md) ⭐⭐⭐
**核心原则**: 指导AI辅助开发的质量标准和工作原则

**七大核心原则**:
1. **诚实守信** - 生成可靠、准确的代码
2. **精益求精** - 追求优雅、高质量的实现
3. **勤勉尽责** - 完整解决问题,不留隐患
4. **严谨细致** - 考虑边界,处理异常
5. **持之以恒** - 系统性解决,保持一致
6. **谦虚谨慎** - 遵守规范,审慎决策
7. **创新进取** - 应用最佳实践,持续改进

**内容**:
- 每个原则的内涵和实践指南
- 生成代码时的检查清单
- 禁止的行为清单
- 具体规范的应用案例

---

## 📚 规范文档列表

### [common-coding.md](./common-coding.md) ⭐
**适用范围**: 所有语言和技术栈

**章节目录**:
1. 代码质量基本要求
2. 集合处理规范
3. 命名规范
4. 常量定义规范
5. 日期时间处理规范
6. 并发处理规范
7. 异常处理规范

**重要规范**:
- Git版本管理规范(使用git mv)
- 集合判空使用Apache Commons库
- 禁止使用魔法值
- 线程池创建规范

---

### [database-standards.md](./database-standards.md) ⭐
**适用范围**: MySQL数据库设计与使用

**章节目录**:
1. 表设计规范(表命名、字段定义、必备字段)
2. 索引规范(索引命名、索引使用)
3. SQL编写规范(查询、更新、JOIN)
4. 性能优化规范

**重要规范**:
- 表必须包含的基础字段(id、create_time、update_time、is_deleted)
- 金额字段使用DECIMAL类型
- 索引命名和使用规范
- 禁止SELECT *

---

### [java-coding-standards.md](./java-coding-standards.md) ⭐
**适用范围**: Java后端项目(Spring Boot)

**章节目录**:
1. Java版本与特性
2. 项目构建配置
3. Gradle编译检查约束（含VO类型校验）⭐
4. 代码风格规范（含Enum枚举类）
5. 依赖管理规范
6. 测试规范

**重要章节**:
- 第3章: Gradle编译检查约束 - 强制性VO类型规范
- 第4.4章: Result和PageResult使用规范 - 禁止嵌套使用
- 第4.5章: 枚举类(Enum)使用规范
- 第6.3章: Mock注解使用规范 - 禁止使用过时的@MockBean

---

### [java21-standards.md](./java21-standards.md) ⭐
**适用范围**: Java 21项目开发

**章节目录**:
1. Java 21简介
2. 版本要求
3. 推荐使用的Java 21特性
4. Virtual Threads(虚拟线程)
5. Record Classes(记录类)
6. Pattern Matching(模式匹配)
7. Sealed Classes(密封类)
8. Text Blocks(文本块)
9. Switch表达式
10. 其他新特性
11. 最佳实践

**重要特性**:
- Virtual Threads: 高并发I/O场景
- Record Classes: DTO/VO定义
- Pattern Matching: 类型判断简化
- Switch表达式: 多分支逻辑

---

### [gradle-standards.md](./gradle-standards.md)
**适用范围**: Gradle 9.0+ 项目

**章节目录**:
1. Gradle版本要求
2. 项目结构规范
3. 编译器配置
4. 依赖管理
5. 注解处理器配置
6. 自定义任务
7. 构建优化
8. 发布配置
9. 常用命令
10. 最佳实践

---

### [maven-standards.md](./maven-standards.md)
**适用范围**: Maven 3.9+ 项目

**章节目录**:
1. Maven版本要求
2. 项目结构规范
3. POM文件规范
4. 依赖管理
5. 插件配置
6. 多模块项目
7. Profile配置
8. 发布配置
9. 常用命令
10. 最佳实践

---

### [mybatis-flex-standards.md](./mybatis-flex-standards.md)
**适用范围**: MyBatis-Flex 1.11.1 + Spring Boot项目

**章节目录**:
1. MyBatis-Flex简介
2. 依赖配置
3. 实体类规范
4. Mapper接口规范
5. 枚举映射
6. 查询构造器(QueryWrapper)
7. 分页查询
8. 逻辑删除
9. 乐观锁
10. 多数据源配置
11. 代码生成器
12. 最佳实践

---

### [frontend-standards.md](./frontend-standards.md) ⭐
**适用范围**: 前端项目开发(React/Vue/TypeScript)

**章节目录**:
1. 技术栈要求
2. 包管理器规范(强制使用pnpm)⭐
3. 项目结构规范
4. 代码风格规范
5. TypeScript规范
6. React开发规范
7. Vue开发规范
8. 样式规范
9. API请求规范
10. 性能优化
11. 测试规范
12. 最佳实践

**重要规范**:
- 强制使用pnpm,禁止使用npm/yarn
- TypeScript强类型约束
- CSS Modules样式隔离
- TanStack Query数据请求

---

## 🎯 快速查找指南

### 按场景索引

| 场景 | 查看文档 |
|------|---------|
| 创建Java类（Entity/VO/DTO/Service等） | [java-coding-standards.md#4.1](./java-coding-standards.md) |
| 创建枚举类(Enum) | [java-coding-standards.md#4.4](./java-coding-standards.md) |
| 定义VO/DTO类 | [java-coding-standards.md#3.1](./java-coding-standards.md) |
| 使用Lombok注解 | [java-coding-standards.md#42-lombok使用规范](./java-coding-standards.md) |
| 使用Record类 | [java-coding-standards.md#43-record类使用规范](./java-coding-standards.md) |
| Result和PageResult使用 | [java-coding-standards.md#44-result和pageresult使用规范](./java-coding-standards.md) |
| VO类型校验失败 | [java-coding-standards.md#9-常见问题排查](./java-coding-standards.md) |
| 编译/依赖问题 | [java-coding-standards.md#9](./java-coding-standards.md) |
| 集合判空处理 | [common-coding.md#集合处理规范](./common-coding.md) |
| 命名规范(类/方法/变量/常量) | [common-coding.md#命名规范](./common-coding.md) |
| 日期时间处理 | [common-coding.md#日期时间处理规范](./common-coding.md) |
| 创建线程池 | [common-coding.md#并发处理规范](./common-coding.md) |
| 异常处理 | [common-coding.md#异常处理规范](./common-coding.md) |
| 设计数据库表 | [database-standards.md#表设计规范](./database-standards.md) |
| 创建索引 | [database-standards.md#索引规范](./database-standards.md) |
| 编写SQL语句 | [database-standards.md#SQL编写规范](./database-standards.md) |
| SQL性能优化 | [database-standards.md#性能优化规范](./database-standards.md) |
| 使用Java 21特性 | [java21-standards.md](./java21-standards.md) |
| Virtual Threads使用 | [java21-standards.md#4-virtual-threads虚拟线程](./java21-standards.md) |
| Record类定义 | [java21-standards.md#5-record-classes记录类](./java21-standards.md) |
| Gradle构建配置 | [gradle-standards.md](./gradle-standards.md) |
| Maven构建配置 | [maven-standards.md](./maven-standards.md) |
| MyBatis-Flex使用 | [mybatis-flex-standards.md](./mybatis-flex-standards.md) |
| QueryWrapper查询 | [mybatis-flex-standards.md#6-查询构造器querywrapper](./mybatis-flex-standards.md) |
| 前端项目初始化 | [frontend-standards.md#2-包管理器规范](./frontend-standards.md) |
| pnpm使用 | [frontend-standards.md#21-强制使用pnpm](./frontend-standards.md) |
| React组件开发 | [frontend-standards.md#6-react开发规范](./frontend-standards.md) |
| Vue组件开发 | [frontend-standards.md#7-vue开发规范](./frontend-standards.md) |
| TypeScript类型定义 | [frontend-standards.md#5-typescript规范](./frontend-standards.md) |
| API请求封装 | [frontend-standards.md#9-api请求规范](./frontend-standards.md) |
| 调试问题 | [debug.md#调试流程](./debug.md) |
| 添加调试日志 | [debug.md#日志添加规范](./debug.md) |
| 分析错误原因 | [debug.md#调试案例](./debug.md) |
| 编写测试用例 | [java-coding-standards.md#6-测试规范](./java-coding-standards.md) |
| Mock测试依赖 | [java-coding-standards.md#63-mock注解使用规范](./java-coding-standards.md) |

---

## ⚠️ 强制性规范速查

### 通用规范
- **Git操作**: 文件移动/重命名必须使用 `git mv` 而不是 `mv`
- **集合判空**: 必须使用 `CollectionUtils.isEmpty()` 而不是 `size() == 0`
- **魔法值**: 禁止在代码中出现魔法值，必须定义为常量
- **线程池**: 必须使用 `ThreadPoolExecutor` 创建，不允许使用 `Executors`
- **调试方法**: 禁止猜测式调试，必须基于证据分析，证据不足时通过日志定位

### Java规范
- **VO类型约束**（编译时强制检查）:
  - **禁止**: `BigDecimal`, `Long`(ID字段), `LocalDateTime`, `LocalDate`, `Date`, `Timestamp`
  - **必须**: 全部使用 `String` 类型
  - **详细**: [java-coding-standards.md#3.1](./java-coding-standards.md)
- **Result和PageResult使用**:
  - **禁止**: 使用 `Result<PageResult<T>>` 嵌套包装
  - **必须**: 分页查询直接返回 `PageResult<T>`
  - **详细**: [java-coding-standards.md#44-result和pageresult使用规范](./java-coding-standards.md)
- **命名规范**:
  - **枚举类**: 以`Enum`结尾（如`PaymentStatusEnum`）
  - **VO类**: 以`VO`结尾（如`PaymentVO`）
  - **Service实现**: 以`ServiceImpl`结尾（如`PaymentServiceImpl`）
- **测试Mock注解**:
  - **禁止**: 使用过时的 `@MockBean` 和 `@SpyBean`
  - **必须**: 使用 `@MockitoBean` 和 `@MockitoSpyBean`
  - **详细**: [java-coding-standards.md#63-mock注解使用规范](./java-coding-standards.md)

### 数据库规范
- **必备字段**: 每张表必须包含 `id`、`create_time`、`update_time`、`is_deleted`
- **金额字段**: 必须使用 `DECIMAL` 类型，禁止使用 `FLOAT` 或 `DOUBLE`
- **时间字段**: 使用 `DATETIME` 类型，不要使用 `TIMESTAMP`
- **查询规范**: 禁止使用 `SELECT *`，必须明确指定字段

### 前端规范
- **包管理器**: 强制使用 `pnpm`，禁止使用 `npm` 或 `yarn`
- **类型安全**: 必须使用TypeScript，避免使用 `any` 类型
- **样式隔离**: 推荐使用CSS Modules，避免全局样式污染
- **组件规范**: 使用函数组件 + Hooks，避免使用类组件

---

**最后更新**: 2025-10-01
