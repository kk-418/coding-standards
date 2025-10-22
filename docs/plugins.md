# 插件目录

本文档列出了 coding-standards 套件中的所有 6 个插件及其详细说明。

---

## 1. work-guidelines

**工作规范与调试方法论**

### 基本信息

- **插件名称**: `work-guidelines`
- **版本**: 1.0.0
- **分类**: 开发流程

### 描述

提供工作规范、代码质量原则和调试方法论,帮助开发团队建立统一的工作标准。

### 核心内容

#### 工作流程规范
- 作者信息统一使用"kk"
- 输出语言统一使用简体中文
- 临时文件管理规则
- Git 操作规范

#### 七大代码质量原则
1. **正面解决问题**:直接处理根本原因,而非绕过问题
2. **拒绝魔法值**:所有常量必须定义
3. **集合处理规范**:使用 CollectionUtils.isEmpty()
4. **线程池管理**:使用 ThreadPoolExecutor,禁用 Executors
5. **Git 操作安全**:文件移动必须用 git mv
6. **禁止猜测式调试**:基于证据进行调试
7. **完整性原则**:实现完整解决方案,而非部分修复

#### 调试方法论
- 禁止猜测,必须基于证据
- 系统性排查问题
- 记录调试过程

### 使用场景

- 开始新项目或任务时
- 需要调试复杂问题时
- 代码审查和质量检查
- 团队规范培训

### Skills

- `work-guidelines`: 包含完整的工作规范文档

---

## 2. java-standards

**Java/Spring Boot 编码规范**

### 基本信息

- **插件名称**: `java-standards`
- **版本**: 1.0.0
- **分类**: 后端开发

### 描述

提供 Java 21 + Spring Boot 项目的完整编码规范,包含强制性类型约束、日志使用、测试规范等。

### 核心内容

#### VO 类型约束(强制)
- ✅ 所有字段使用 `String` 类型
- ❌ 禁止 `BigDecimal`、`Long`(ID字段)、`LocalDateTime`、`LocalDate`

#### 日志使用(强制)
- ✅ 使用 `@Slf4j` 注解 + SLF4J
- ❌ 禁止 `System.out.println()` 和 `System.err.println()`

#### Result 使用(强制)
- ✅ 分页查询直接返回 `PageResult<T>`
- ❌ 禁止 `Result<PageResult<T>>` 嵌套

#### 命名规范
- 枚举类以 `Enum` 结尾
- VO 类以 `VO` 结尾
- Service 实现以 `ServiceImpl` 结尾

#### Java 21 特性
- Virtual Threads(虚拟线程)
- Record Classes(记录类)
- Pattern Matching(模式匹配)
- Switch 表达式

#### 测试规范
- 使用 `@MockitoBean`,禁止 `@MockBean`
- 单元测试覆盖率 ≥ 80%
- 集成测试覆盖率 ≥ 70%
- 命名: `should_<预期结果>_when_<测试条件>`

### 使用场景

- 创建 Java 类(Entity/VO/DTO/Service等)
- 使用 Lombok 或 Record 类
- 编写 Spring Boot 应用
- 编写单元测试和集成测试

### Commands

- `/coding-standards:java` - 快速加载 Java 编码规范
- `/coding-standards:check-vo` - 检查 VO 类型约束
- `/coding-standards:check-logging` - 检查日志使用规范

### Skills

- `java-standards`: 包含 Java 编码规范、Java 21 特性、测试规范

---

## 3. build-tools

**Gradle/Maven 构建工具规范**

### 基本信息

- **插件名称**: `build-tools`
- **版本**: 1.0.0
- **分类**: 构建工具

### 描述

提供 Gradle 9.0+ 和 Maven 3.9+ 的配置规范、依赖管理和构建最佳实践。

### 核心内容

#### Gradle 规范
- Kotlin DSL 优先于 Groovy
- 合理使用 Build Cache
- 依赖版本管理(Version Catalog)
- 多模块项目配置

#### Maven 规范
- POM 文件结构规范
- 依赖管理最佳实践
- Profile 配置
- 插件版本管理

#### 通用规范
- 依赖冲突解决
- 私有仓库配置
- 构建优化技巧

### 使用场景

- 配置项目构建
- 管理依赖关系
- 设置编译选项
- 优化构建性能

### Skills

- `build-tools`: 包含 Gradle 规范和 Maven 规范

---

## 4. database

**MySQL + MyBatis-Flex 数据库规范**

### 基本信息

- **插件名称**: `database`
- **版本**: 1.0.0
- **分类**: 数据库设计

### 描述

提供 MySQL 数据库设计规范和 MyBatis-Flex 使用标准,包含强制性约束。

### 核心内容

#### 表设计规范(强制)
- 必备字段: `id`, `create_time`, `update_time`, `is_deleted`
- 金额字段使用 `DECIMAL`,禁止 `FLOAT`/`DOUBLE`
- 主键使用 `BIGINT UNSIGNED`
- 字符集统一 `utf8mb4`

#### 查询规范(强制)
- 禁止 `SELECT *`
- 合理使用索引
- 避免深分页

#### MyBatis-Flex 规范(强制)
- Service 层必须继承 `ServiceImpl`
- 更新操作使用 `UpdateChain`,避免 `updateById`
- 批量操作使用 `saveBatch` 或 `updateBatchById`

#### 性能优化
- 索引设计原则
- 慢查询优化
- 连接池配置

### 使用场景

- 设计数据库表
- 使用 MyBatis-Flex ORM
- 更新数据库记录
- 性能调优

### Commands

- `/coding-standards:check-mybatis` - 检查 MyBatis-Flex 使用规范

### Skills

- `database`: 包含数据库设计规范和 MyBatis-Flex 规范

---

## 5. frontend

**React/Vue + TypeScript 前端开发规范**

### 基本信息

- **插件名称**: `frontend`
- **版本**: 1.0.0
- **分类**: 前端开发

### 描述

提供现代前端开发规范,涵盖 React、Vue 和 TypeScript 的最佳实践。

### 核心内容

#### 包管理器(强制)
- ✅ 强制使用 `pnpm`
- ❌ 禁止 `npm` 和 `yarn`

#### TypeScript 规范(强制)
- 避免 `any` 类型
- 合理使用泛型
- 接口优先于类型别名

#### React 规范
- 使用函数组件 + Hooks
- 避免类组件
- 合理使用 useMemo 和 useCallback

#### Vue 规范
- 使用 Composition API
- 合理使用 Ref 和 Reactive
- 组件命名规范

#### 代码风格
- ESLint + Prettier 配置
- 命名规范
- 文件组织

### 使用场景

- 开发 React/Vue 项目
- 配置 TypeScript
- 创建前端组件
- 代码审查

### Commands

- `/coding-standards:frontend` - 快速加载前端开发规范

### Skills

- `frontend`: 包含完整的前端开发规范

---

## 6. common

**通用编码规范**

### 基本信息

- **插件名称**: `common`
- **版本**: 1.0.0
- **分类**: 通用规范

### 描述

提供跨语言适用的通用编码规范,包含命名、集合处理、异常处理等基础标准。

### 核心内容

#### 命名规范
- **类名**: UpperCamelCase(大驼峰)
- **方法名**: lowerCamelCase(小驼峰)
- **常量**: UPPER_SNAKE_CASE(大写下划线)
- **变量**: lowerCamelCase(小驼峰)

#### 集合处理
- 判空使用 `CollectionUtils.isEmpty()`
- 合理使用 Stream API
- 避免并发修改异常

#### 日期时间
- 使用 `LocalDateTime` 而非 `Date`
- 时区处理规范
- 日期格式化

#### 并发处理
- 线程池使用 `ThreadPoolExecutor`
- 线程安全集合
- 锁的使用规范

#### 异常处理
- 异常分类(检查型/非检查型)
- 自定义异常规范
- 异常日志记录

### 使用场景

- 跨语言编码标准参考
- 命名规范查询
- 异常处理指导
- 并发编程

### Commands

- `/coding-standards:check-naming` - 检查命名规范

### Skills

- `common`: 包含通用编码规范文档

---

## 安装指南

### 安装所有插件

```bash
# 克隆仓库
git clone https://github.com/kk-418/coding-standards.git ~/.claude/plugins/coding-standards

# 或通过插件市场安装(推荐)
/plugin marketplace add kk-418/coding-standards
/plugin install coding-standards
```

### 按需安装插件

Claude Code 会根据上下文自动加载相关插件,无需手动配置。

---

## 插件依赖关系

- `work-guidelines` 是基础插件,建议所有项目安装
- `common` 提供通用规范,与其他插件配合使用
- 其他插件根据技术栈按需使用

---

**作者**: kk
**版本**: 2.0.0
**最后更新**: 2025-10-22
