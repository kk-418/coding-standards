# 变更日志 (Changelog)

本文档记录编码规范插件的所有重要变更。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/),
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [1.0.0] - 2025-10-22

### 🎉 首次发布

#### 新增 (Added)

**插件架构:**
- ✨ 创建 Claude Code 插件基础架构
- 📦 配置 plugin.json 插件元数据
- 🔧 使用 `coding-standards:` 命名空间

**Skills (6个):**
- ✨ `work-guidelines` - 工作规范与调试方法论
  - 包含工作流程规范(作者信息、输出语言等)
  - 七大代码质量原则
  - 禁止猜测式调试方法论
- ✨ `java-standards` - Java/Spring Boot 编码规范
  - VO 类型约束(强制使用 String)
  - 日志使用规范(禁止 System.out.println)
  - Result 和 PageResult 使用规范
  - Java 21 特性使用指南
  - 测试规范(Mock 注解、覆盖率要求)
- ✨ `build-tools` - Gradle/Maven 构建规范
  - Gradle 9.0+ 配置规范
  - Maven 3.9+ 配置规范
  - 依赖管理最佳实践
- ✨ `database` - 数据库设计与 MyBatis-Flex 规范
  - MySQL 数据库设计规范
  - MyBatis-Flex 1.11.1+ 使用规范
  - Service 层继承规范
  - UpdateChain 更新操作规范
- ✨ `frontend` - 前端开发规范
  - React/Vue + TypeScript 规范
  - pnpm 包管理器强制使用
  - 组件开发规范(函数组件 + Hooks)
  - CSS Modules 样式规范
- ✨ `common` - 通用编码规范
  - 集合处理规范
  - 命名规范(类/方法/变量/常量)
  - 日期时间处理规范
  - 并发处理规范
  - 异常处理规范

**Slash Commands (6个):**

快捷命令:
- ✨ `/coding-standards:java` - 快速加载 Java 编码规范
- ✨ `/coding-standards:frontend` - 快速加载前端开发规范

检查命令:
- ✨ `/coding-standards:check-vo` - 检查 VO 类型约束
- ✨ `/coding-standards:check-naming` - 检查命名规范
- ✨ `/coding-standards:check-logging` - 检查日志使用
- ✨ `/coding-standards:check-mybatis` - 检查 MyBatis-Flex 规范

**文档:**
- 📝 README.md - 完整的插件使用文档
- 📝 CLAUDE.md - 插件开发和维护文档
- 📝 CHANGELOG.md - 本变更日志
- 📄 LICENSE - MIT 开源许可证

#### 规范来源

- Java 编码规范基于《阿里巴巴 Java 开发规范(嵩山版)》整理优化
- 前端规范参考 Airbnb JavaScript Style Guide
- 数据库规范基于 MySQL 官方最佳实践

#### 技术栈

- Java 21+
- Spring Boot 3.x
- MyBatis-Flex 1.11.1+
- Gradle 9.0+ / Maven 3.9+
- React 18+ / Vue 3+
- TypeScript 5+

---

## [Unreleased]

### 计划新增 (Planned)

- [ ] 添加 Python 编码规范 Skill
- [ ] 添加 Go 编码规范 Skill
- [ ] 添加更多检查命令
- [ ] 集成代码格式化工具配置
- [ ] 添加更多实际项目示例

### 已知问题 (Known Issues)

暂无

---

## 版本说明

### 版本号规则

遵循语义化版本控制 2.0.0:

- **MAJOR** (主版本号): 不兼容的 API 变更
- **MINOR** (次版本号): 向后兼容的功能新增
- **PATCH** (修订号): 向后兼容的问题修复

### 变更类型

- **Added** (新增): 新功能
- **Changed** (变更): 现有功能的变更
- **Deprecated** (弃用): 即将移除的功能
- **Removed** (移除): 已移除的功能
- **Fixed** (修复): Bug 修复
- **Security** (安全): 安全问题修复

---

**维护者**: kk
**最后更新**: 2025-10-22
