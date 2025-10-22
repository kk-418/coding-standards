# 更新日志 (Changelog)

本文档记录 coding-standards 套件的所有重要变更。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/),
版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

---

## [2.0.0] - 2025-10-22

### 🏗️ 重大重构

**架构变更**: 从单体插件改造为 Marketplace 模式

#### 新增 (Added)

- 🏪 **Marketplace 架构**: 采用 marketplace.json 统一管理 6 个插件
- 📁 **插件目录**: 新增 `plugins/` 目录,按技术栈组织插件
  - work-guidelines/
  - java-standards/
  - build-tools/
  - database/
  - frontend/
  - common/
- 📚 **文档目录**: 新增 `docs/` 目录
  - plugins.md - 插件详细说明
  - skills.md - Skills 总览
  - usage.md - 完整使用指南
- 🔗 **参考架构**: 参考 wshobson/agents 的 Marketplace 模式设计

#### 变更 (Changed)

- 🔄 **目录重组**:
  - `skills/` → `plugins/*/skills/`
  - `commands/` → `plugins/*/commands/`
- 📝 **配置文件**:
  - 新增 `.claude-plugin/marketplace.json`
  - 保留 `.claude-plugin/plugin.json` (标记为废弃)
- 📖 **README.md**: 完全重写,适应 Marketplace 架构
- 📄 **CLAUDE.md**: 更新维护指南,说明新架构

#### 废弃 (Deprecated)

- ⚠️ `.claude-plugin/plugin.json` - 将在未来版本移除,请使用 marketplace.json

#### 技术细节

- 使用 `git mv` 迁移所有文件,保留 Git 历史
- 插件命名空间保持 `coding-standards:` 前缀
- Slash Commands 保持原有命名,无破坏性变更
- Skills 自动触发机制保持不变

#### 迁移指南

用户无需任何操作,更新后自动适配新架构:

```bash
# 拉取最新版本
cd ~/.claude/plugins/coding-standards
git pull origin main
```

---

## [1.0.0] - 2025-10-22

### 初始发布

#### 新增 (Added)

- 🎯 **6 个 Skills**: 按技术栈组织的编码规范
  - work-guidelines - 工作规范与调试方法论
  - java-standards - Java/Spring Boot 编码规范
  - build-tools - Gradle/Maven 构建规范
  - database - MySQL + MyBatis-Flex 规范
  - frontend - React/Vue + TypeScript 规范
  - common - 通用编码规范

- ⚡ **6 个 Slash Commands**:
  - `/coding-standards:java` - 快速加载 Java 规范
  - `/coding-standards:frontend` - 快速加载前端规范
  - `/coding-standards:check-vo` - 检查 VO 类型约束
  - `/coding-standards:check-naming` - 检查命名规范
  - `/coding-standards:check-logging` - 检查日志使用
  - `/coding-standards:check-mybatis` - 检查 MyBatis-Flex 规范

- 📚 **18 个规范文档**:
  - 工作规范: guidelines.md, values.md, debug.md
  - Java 规范: java-coding-standards.md, java21-standards.md, java-test-standards.md
  - 构建工具: gradle-standards.md, maven-standards.md
  - 数据库: database-standards.md, mybatis-flex-standards.md
  - 前端: frontend-standards.md
  - 通用: common-coding.md

- 🔧 **配置文件**:
  - .claude-plugin/plugin.json
  - CLAUDE.md (维护指南)
  - README.md (使用文档)
  - LICENSE (MIT)

#### 核心规范

**Java 强制性规范:**
- VO 类型约束: 所有字段使用 String
- 日志使用: @Slf4j,禁止 System.out.println
- Result 使用: 分页直接返回 PageResult<T>
- 测试 Mock: 使用 @MockitoBean

**数据库强制性规范:**
- 必备字段: id, create_time, update_time, is_deleted
- Service 层: 必须继承 ServiceImpl
- 更新操作: 使用 UpdateChain

**前端强制性规范:**
- 包管理器: 强制 pnpm
- 类型安全: 避免 any 类型
- 组件规范: 函数组件 + Hooks

**通用规范:**
- Git 操作: 文件移动使用 git mv
- 集合判空: 使用 CollectionUtils.isEmpty()
- 调试方法: 禁止猜测,基于证据

---

## 版本对比

| 版本 | 架构 | 插件数 | Skills | Commands | 文档 |
|------|------|--------|--------|----------|------|
| 2.0.0 | Marketplace | 6 | 6 | 6 | 21 (新增3个docs) |
| 1.0.0 | 单体插件 | 1 | 6 | 6 | 18 |

---

## 未来计划

### v2.1.0 (计划中)

- [ ] 新增 Python 编码规范插件
- [ ] 新增 Go 编码规范插件
- [ ] 完善 CI/CD 自动化检查
- [ ] 提供在线文档站点

### v2.2.0 (计划中)

- [ ] 集成 ESLint/Prettier 配置模板
- [ ] 集成 Checkstyle 配置模板
- [ ] 新增代码审查清单

---

## 链接

- [GitHub 仓库](https://github.com/kk-418/coding-standards)
- [问题反馈](https://github.com/kk-418/coding-standards/issues)
- [贡献指南](https://github.com/kk-418/coding-standards/blob/main/CLAUDE.md)

---

**格式说明**:
- 🏗️ 重大重构
- 🎯 新功能
- ⚡ 性能优化
- 🐛 Bug 修复
- 📚 文档更新
- 🔧 配置变更
- ⚠️ 废弃警告
