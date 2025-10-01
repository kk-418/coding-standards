# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## 代码库概述

这是一个编码规范文档库,为 AI 辅助开发提供完整的编码标准和最佳实践指南。所有规范文档使用 Markdown 格式编写,并通过 index.md 提供索引和快速查找功能。README.md 是给用户阅读的使用文档。

**核心文件**:
- `index.md` - 规范文档索引和快速查找指南
- `README.md` - 使用文档和快速入门指南
- `guidelines.md` - AI 工作规范和流程要求
- `values.md` - 代码质量价值观和核心原则
- `debug.md` - 调试方法论(禁止猜测式调试)

**技术规范文档**:
- `common-coding.md` - 通用编码规范(所有语言)
- `java-coding-standards.md` - Java/Spring Boot 规范
- `java21-standards.md` - Java 21 特性使用规范
- `gradle-standards.md` - Gradle 构建规范
- `maven-standards.md` - Maven 构建规范
- `database-standards.md` - MySQL 数据库规范
- `mybatis-flex-standards.md` - MyBatis-Flex ORM 规范
- `frontend-standards.md` - 前端开发规范(React/Vue/TypeScript)

---

## 架构设计

### 文档组织结构

```
coding-standards/
├── index.md              # 📋 规范索引(包含场景查找表)
├── README.md             # 📖 使用文档(快速入门)
├── guidelines.md         # 🔧 工作流程规范
├── values.md              # 🌟 质量价值观
├── debug.md              # 🐛 调试方法论
├── common-coding.md      # 📚 通用规范
├── java-*.md             # ☕ Java相关规范
├── *-standards.md        # 📖 各技术栈规范
└── CLAUDE.md             # 🤖 本文件
```

### 核心设计理念

1. **文档分离**:
   - `index.md` - AI 使用的规范索引,提供场景查找表(主要给 Claude 使用)
   - `README.md` - 用户阅读的使用文档,提供快速入门和说明(给人类阅读)
2. **分层设计**:
   - 第一层: 工作方法(guidelines, values, debug)
   - 第二层: 通用规范(common-coding)
   - 第三层: 技术栈规范(java, frontend, database等)
3. **强制约束**: 部分规范通过编译时检查强制执行(如 VO 类型校验)
4. **证据驱动**: 调试规范要求基于证据分析,禁止猜测

---

## 文档维护规范

### 添加新规范文档

1. 在对应的 `*-standards.md` 文件中添加规范内容
2. 在 `index.md` 的"规范文档列表"中添加文档说明
3. 在 `index.md` 的"按场景索引"表格中添加场景链接
4. 如果是强制性规范,添加到 `index.md` 的"强制性规范速查"部分
5. 如果需要,在 `README.md` 中添加使用示例

### 文档格式要求

```markdown
# 规范名称

> **作者**: kk
> **创建日期**: YYYY-MM-DD
> **用途**: 简短描述文档用途

---

## 章节1

内容...

---

## 章节2

内容...

---

**最后更新**: YYYY-MM-DD
```

### 强制性规范标记

使用星级(⭐)标记重要程度:
- ⭐⭐⭐ - 核心规范,必须遵守
- ⭐ - 重要规范,强烈推荐

在 README.md 中的强制性规范必须使用以下格式:
```markdown
- **规范名称**: 规范说明
  - **禁止**: xxx
  - **必须**: xxx
  - **详细**: [链接到详细文档]
```

---

## 核心规范速查

### 必须遵守的工作规范

1. **作者信息**: 统一使用 "kk" 作为作者
2. **输出语言**: 使用简体中文(技术术语除外)
3. **正面解决**: 直接解决根本原因,不绕过问题
4. **临时文件**: 放在项目 temp 目录
5. **调试方法**: 禁止猜测,必须基于证据

### 七大质量原则

1. **诚实守信** - 生成可靠、准确的代码
2. **精益求精** - 追求优雅、高质量的实现
3. **勤勉尽责** - 完整解决问题,不留隐患
4. **严谨细致** - 考虑边界,处理异常
5. **持之以恒** - 系统性解决,保持一致
6. **谦虚谨慎** - 遵守规范,审慎决策
7. **创新进取** - 应用最佳实践,持续改进

### 调试黄金法则

1. **基于证据,不靠猜测** - 所有结论必须有证据支撑
2. **逐步缩小范围** - 从表象到根因,一步步验证
3. **系统性思考** - 考虑数据流、调用链路、边界条件
4. **记录调试过程** - 形成问题解决的知识库

---

## 常见任务

### 查找特定规范

使用 index.md 中的"按场景索引"表格快速查找:

```bash
# 示例:需要了解 Java VO 类型规范
# 1. 打开 index.md
# 2. 查找"按场景索引"表格
# 3. 找到"定义VO/DTO类"行
# 4. 点击链接跳转到 java-coding-standards.md#3.1
```

**注意**: README.md 是给用户阅读的使用说明文档,不是规范索引。

### 添加新的编码规范

```bash
# 1. 确定规范所属类别(通用/Java/前端/数据库等)
# 2. 编辑对应的 *-standards.md 文件
# 3. 添加规范内容(包含示例和反例)
# 4. 更新 README.md 索引
```

### 验证规范完整性

检查清单:
- [ ] 规范文档包含作者、日期、用途
- [ ] 规范内容包含示例代码
- [ ] 强制性规范有明确的"禁止"和"必须"说明
- [ ] index.md 场景索引表已更新
- [ ] 强制性规范已添加到 index.md 速查部分
- [ ] 必要时在 README.md 中添加使用示例

---

## 重要约定

### 文档引用

在其他项目的 CLAUDE.md 中引用本规范库:

```markdown
# CLAUDE Entry
@RULES.md
@coding-standards/index.md
```

**注意**: 引用时使用 `index.md` 而不是 `README.md`。README.md 是给用户阅读的文档。

### 规范优先级

当遇到规范冲突时,优先级顺序:
1. 项目特定规范(项目 CLAUDE.md)
2. 强制性编码规范(coding-standards)
3. 通用最佳实践

### 规范更新

- 修改规范时必须更新"最后更新"日期
- 新增强制性规范必须在 README.md 中标记
- 废弃的规范移至文档末尾的"已废弃"章节

---

## 与其他工具集成

### IDE 集成

规范文档可直接在 IDE 中查看:
- VSCode: 使用 Markdown Preview
- IntelliJ IDEA: 支持 Markdown 插件

### Git Hooks

建议在项目中配置 pre-commit hook 检查:
- Java VO 类型约束
- 前端包管理器约束(pnpm)
- 文件命名规范

---

**最后更新**: 2025-10-01
**维护者**: kk
