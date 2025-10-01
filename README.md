# 编码规范使用文档

> **作者**: kk
> **创建日期**: 2025-10-01
> **用途**: 说明如何使用编码规范文档库

---

## 📖 文档库简介

这是一个为 AI 辅助开发设计的编码规范文档库,包含完整的编码标准、最佳实践和调试方法论。

**规范来源**:
- Java 编码规范基于《阿里巴巴 Java 开发规范(嵩山版)》进行整理和优化

**适用场景**:
- AI (Claude/ChatGPT/Copilot) 辅助编码
- 团队编码规范统一
- 代码审查参考标准
- 新人快速上手指南

---

## 🚀 快速开始

### 1. 查看规范索引

打开 **[index.md](./index.md)** 查看所有可用规范:

```bash
# 在当前目录
open index.md

# 或在浏览器中查看
# file:///path/to/coding-standards/index.md
```

### 2. 场景化查找

在 [index.md](./index.md) 的"按场景索引"表格中快速查找:

| 我想... | 查看文档 |
|---------|---------|
| 创建 Java VO 类 | [java-coding-standards.md#3.1](./java-coding-standards.md) |
| 调试问题 | [debug.md](./debug.md) |
| 设计数据库表 | [database-standards.md](./database-standards.md) |
| 前端项目初始化 | [frontend-standards.md#2](./frontend-standards.md) |

### 3. 在 Claude Code 中使用

在项目的 `.claude/CLAUDE.md` 中引用:

```markdown
# CLAUDE Entry
@RULES.md
@coding-standards/index.md
```

---

## 📚 文档结构

### 核心文档 (必读)

| 文档 | 说明 | 重要性 |
|------|------|--------|
| [index.md](./index.md) | 📋 规范索引和快速查找 | ⭐⭐⭐ |
| [guidelines.md](./guidelines.md) | 🔧 工作规范和流程 | ⭐⭐⭐ |
| [values.md](./values.md) | 🌟 代码质量价值观 | ⭐⭐⭐ |
| [debug.md](./debug.md) | 🐛 调试方法论 | ⭐⭐⭐ |

### 通用规范

| 文档 | 说明 | 适用范围 |
|------|------|---------|
| [common-coding.md](./common-coding.md) | 通用编码规范 | 所有语言 |

### Java 后端规范

| 文档 | 说明 | 技术栈 |
|------|------|--------|
| [java-coding-standards.md](./java-coding-standards.md) | Java 编码规范 | Java 21 + Spring Boot |
| [java21-standards.md](./java21-standards.md) | Java 21 特性 | Java 21 |
| [gradle-standards.md](./gradle-standards.md) | Gradle 构建 | Gradle 9.0+ |
| [maven-standards.md](./maven-standards.md) | Maven 构建 | Maven 3.9+ |
| [mybatis-flex-standards.md](./mybatis-flex-standards.md) | MyBatis-Flex ORM | MyBatis-Flex 1.11.1+ |

### 数据库规范

| 文档 | 说明 | 技术栈 |
|------|------|--------|
| [database-standards.md](./database-standards.md) | 数据库设计规范 | MySQL 8.0+ |

### 前端规范

| 文档 | 说明 | 技术栈 |
|------|------|--------|
| [frontend-standards.md](./frontend-standards.md) | 前端开发规范 | React/Vue + TypeScript |

---

## 🎯 使用场景示例

### 场景 1: 创建 Java 实体类

**需求**: 创建一个支付订单的实体类

**步骤**:
1. 打开 [index.md](./index.md)
2. 查找"创建 Java 类"场景
3. 跳转到 [java-coding-standards.md#4.1](./java-coding-standards.md)
4. 按规范创建实体类

**关键规范**:
- 使用 Lombok 注解简化代码
- 实体类使用 `@Table` 注解
- 金额字段使用 `BigDecimal`
- 时间字段使用 `LocalDateTime`

### 场景 2: 定义 VO 返回类

**需求**: 定义 API 返回的 VO 对象

**步骤**:
1. 查看 [java-coding-standards.md#3.1](./java-coding-standards.md)
2. 遵守 **VO 类型约束** (编译时强制检查)

**关键规范**:
- ✅ 所有字段使用 `String` 类型
- ❌ 禁止 `BigDecimal`, `Long`(ID), `LocalDateTime` 等
- 类名以 `VO` 结尾

### 场景 3: 调试 NPE 问题

**需求**: 代码抛出 NullPointerException

**步骤**:
1. 打开 [debug.md](./debug.md)
2. 遵循"调试流程"章节
3. **禁止猜测**,基于证据分析

**关键步骤**:
1. 收集错误日志和堆栈信息
2. 分析可能的原因
3. 添加日志验证假设
4. 逐步定位根本原因

### 场景 4: 前端项目初始化

**需求**: 初始化一个 React + TypeScript 项目

**步骤**:
1. 查看 [frontend-standards.md#2](./frontend-standards.md)
2. **强制使用 pnpm**,禁止 npm/yarn

**关键规范**:
```bash
# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build
```

---

## ⚠️ 强制性规范速查

### 通用规范
- **Git 操作**: 文件移动/重命名必须使用 `git mv`
- **集合判空**: 必须使用 `CollectionUtils.isEmpty()`
- **魔法值**: 禁止,必须定义为常量
- **线程池**: 必须使用 `ThreadPoolExecutor`,不允许 `Executors`
- **调试方法**: 禁止猜测,必须基于证据

### Java 规范
- **VO 类型约束**: 所有字段使用 `String`,禁止 `BigDecimal`/`Long`/`LocalDateTime`
- **Result 使用**: 禁止 `Result<PageResult<T>>`,直接用 `PageResult<T>`
- **枚举命名**: 以 `Enum` 结尾

### 数据库规范
- **必备字段**: `id`, `create_time`, `update_time`, `is_deleted`
- **金额字段**: 必须用 `DECIMAL`,禁止 `FLOAT`/`DOUBLE`
- **查询规范**: 禁止 `SELECT *`

### 前端规范
- **包管理器**: 强制 `pnpm`,禁止 `npm`/`yarn`
- **类型安全**: 避免 `any` 类型
- **组件规范**: 函数组件 + Hooks

---

## 🔧 与 AI 工具集成

### Claude Code

在项目根目录创建 `.claude/CLAUDE.md`:

```markdown
# CLAUDE Entry
@RULES.md
@coding-standards/index.md
```

### GitHub Copilot

在项目根目录创建 `.github/copilot-instructions.md`:

```markdown
# Copilot 指令

请遵守以下编码规范:

- 参考: coding-standards/index.md
- Java 规范: coding-standards/java-coding-standards.md
- 调试规范: coding-standards/debug.md
```

### Cursor

在项目根目录创建 `.cursor/rules`:

```markdown
# Cursor 规则

## 编码规范

请遵守 coding-standards/ 目录下的所有规范文档。

重点关注:
- VO 类型约束
- 调试方法论(禁止猜测)
- 前端包管理器(pnpm)
```

---

## 📝 维护和更新

### 添加新规范

1. 在对应的 `*-standards.md` 文件中添加内容
2. 在 `index.md` 的场景索引表中添加链接
3. 如果是强制性规范,添加到 `index.md` 的速查部分
4. 更新文档的"最后更新"日期

### 规范格式

```markdown
# 规范标题

> **作者**: kk
> **创建日期**: 2025-10-01
> **用途**: 简短描述

---

## 章节 1

### 规范内容

**推荐做法**:
```java
// ✅ 正确示例
```

**不推荐做法**:
```java
// ❌ 错误示例
```

---

**最后更新**: 2025-10-01
```

---

## ❓ 常见问题

### Q1: 为什么 VO 类型必须用 String?

**A**: 编译时强制检查,避免前后端类型不一致导致的序列化问题。详见 [java-coding-standards.md#3.1](./java-coding-standards.md)。

### Q2: 为什么禁止猜测式调试?

**A**: 猜测浪费时间且可能掩盖真正问题。必须基于证据分析。详见 [debug.md](./debug.md)。

### Q3: 前端为什么强制用 pnpm?

**A**: pnpm 更快、更节省磁盘空间,且依赖管理更严格。详见 [frontend-standards.md#2.1](./frontend-standards.md)。

### Q4: Result 和 PageResult 怎么使用?

**A**: 分页查询直接返回 `PageResult<T>`,不要嵌套 `Result<PageResult<T>>`。详见 [java-coding-standards.md#4.4](./java-coding-standards.md)。

### Q5: 集合判空为什么用 CollectionUtils?

**A**: 自动处理 null 检查,避免 NPE。详见 [common-coding.md#集合处理规范](./common-coding.md)。

---

## 📞 反馈和贡献

### 发现规范问题

如果发现规范有误或需要补充:
1. 记录问题描述
2. 提供具体场景
3. 建议改进方案
4. 联系维护者

### 贡献新规范

欢迎贡献新的编码规范:
1. 按照规范格式编写
2. 包含正确示例和错误示例
3. 添加到 index.md 索引
4. 说明适用场景

---

## 🔗 相关资源

### 官方文档
- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [MyBatis-Flex 官方文档](https://mybatis-flex.com/)
- [React 官方文档](https://react.dev/)
- [TypeScript 官方文档](https://www.typescriptlang.org/)

### 最佳实践
- [阿里巴巴 Java 开发手册](https://github.com/alibaba/p3c)
- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

---

**文档版本**: 1.0.0
**最后更新**: 2025-10-01
**维护者**: kk
