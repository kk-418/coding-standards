# CLAUDE.md

This file provides guidance to Claude Code when working with the Coding Standards Suite.

---

## 套件概述

这是一个采用 **Marketplace 模式**的 Claude Code 编码规范套件,提供中文编码规范和最佳实践。

**套件名称**: coding-standards
**版本**: 2.0.0
**作者**: kk
**架构**: Marketplace (6 个独立插件)
**类型**: Skills + Slash Commands

---

## 架构说明

### Marketplace 模式

参考 [wshobson/agents](https://github.com/wshobson/agents) 的架构设计:

- **单一仓库**: 所有插件在同一仓库,统一版本管理
- **插件隔离**: 6 个插件独立组织,按技术栈划分
- **统一配置**: 通过 `.claude-plugin/marketplace.json` 管理所有插件
- **按需加载**: Claude Code 根据上下文自动加载相关插件

### 插件列表(6个)

1. **work-guidelines**: 工作规范、价值观、调试方法论
2. **java-standards**: Java/Spring Boot 编码规范
3. **build-tools**: Gradle/Maven 构建规范
4. **database**: MySQL + MyBatis-Flex 规范
5. **frontend**: React/Vue + TypeScript 规范
6. **common**: 通用编码规范(跨语言)

每个插件包含:
- **skills/** 目录:包含 1 个 Skill 和相关规范文档
- **commands/** 目录:包含 Slash Commands(可选)

### Slash Commands(6个)

**快捷命令(2个):**
- `/coding-standards:java` - 加载 Java 编码规范
- `/coding-standards:frontend` - 加载前端开发规范

**检查命令(4个):**
- `/coding-standards:check-vo` - 检查 VO 类型约束
- `/coding-standards:check-naming` - 检查命名规范
- `/coding-standards:check-logging` - 检查日志使用
- `/coding-standards:check-mybatis` - 检查 MyBatis-Flex 规范

---

## 目录结构

```
coding-standards/                    # 根目录
├── .claude-plugin/
│   └── marketplace.json            # Marketplace 配置
├── plugins/                         # 插件目录
│   ├── work-guidelines/            # 插件 1
│   │   └── skills/
│   │       └── work-guidelines/
│   │           ├── SKILL.md
│   │           ├── guidelines.md
│   │           ├── values.md
│   │           └── debug.md
│   ├── java-standards/             # 插件 2
│   │   ├── skills/
│   │   │   └── java-standards/
│   │   │       ├── SKILL.md
│   │   │       ├── java-coding-standards.md
│   │   │       ├── java21-standards.md
│   │   │       └── java-test-standards.md
│   │   └── commands/
│   │       ├── java.md
│   │       ├── check-vo.md
│   │       └── check-logging.md
│   ├── build-tools/                # 插件 3
│   │   └── skills/
│   │       └── build-tools/
│   │           ├── SKILL.md
│   │           ├── gradle-standards.md
│   │           └── maven-standards.md
│   ├── database/                   # 插件 4
│   │   ├── skills/
│   │   │   └── database/
│   │   │       ├── SKILL.md
│   │   │       ├── database-standards.md
│   │   │       └── mybatis-flex-standards.md
│   │   └── commands/
│   │       └── check-mybatis.md
│   ├── frontend/                   # 插件 5
│   │   ├── skills/
│   │   │   └── frontend/
│   │   │       ├── SKILL.md
│   │   │       └── frontend-standards.md
│   │   └── commands/
│   │       └── frontend.md
│   └── common/                     # 插件 6
│       ├── skills/
│       │   └── common/
│       │       ├── SKILL.md
│       │       └── common-coding.md
│       └── commands/
│           └── check-naming.md
├── docs/                            # 文档目录
│   ├── plugins.md                   # 插件详细说明
│   ├── skills.md                    # Skills 总览
│   └── usage.md                     # 使用指南
├── README.md                        # 套件总览
├── CHANGELOG.md                     # 版本日志
├── LICENSE                          # MIT 许可证
└── CLAUDE.md                        # 本文件(维护指南)
```

---

## Skills 触发机制

Claude Code 会根据用户请求的上下文自动加载相关 Skills:

### work-guidelines 触发条件
- 用户开始新项目/任务
- 需要调试复杂问题
- 询问代码质量标准
- 关键词: "工作规范", "调试", "质量原则"

### java-standards 触发条件
- 创建 Java 类(Entity/VO/DTO/Service等)
- 使用 Lombok 或 Record
- 编写 Spring Boot 代码
- 编写 Java 测试
- 关键词: "Java", "Spring Boot", "VO", "Entity"

### build-tools 触发条件
- 配置 Gradle/Maven
- 管理项目依赖
- 设置编译选项
- 关键词: "Gradle", "Maven", "构建", "依赖"

### database 触发条件
- 设计数据库表
- 使用 MyBatis-Flex
- 编写 SQL 语句
- 更新数据库记录
- 关键词: "数据库", "表", "MyBatis", "SQL"

### frontend 触发条件
- 开发 React/Vue 项目
- 配置 TypeScript
- 创建前端组件
- 关键词: "React", "Vue", "前端", "组件", "TypeScript"

### common 触发条件
- 需要跨语言编码标准
- 命名规范查询
- 异常处理指导
- 关键词: "命名", "集合", "异常处理"

---

## 核心强制性规范

### 通用规范
- **Git 操作**: 文件移动必须用 `git mv`
- **集合判空**: 用 `CollectionUtils.isEmpty()`
- **魔法值**: 禁止,定义为常量
- **线程池**: 用 `ThreadPoolExecutor`
- **调试**: 禁止猜测,基于证据

### Java 规范
- **VO 类型**: 全用 `String`,禁 `BigDecimal`/`Long`/`LocalDateTime`
- **日志**: 用 `@Slf4j`,禁 `System.out.println`
- **Result**: 分页直接返 `PageResult<T>`
- **命名**: 枚举以 `Enum` 结尾

### 数据库规范
- **必备字段**: `id`, `create_time`, `update_time`, `is_deleted`
- **金额**: 用 `DECIMAL`
- **查询**: 禁 `SELECT *`
- **Service**: 继承 `ServiceImpl`
- **更新**: 用 `UpdateChain`

### 前端规范
- **包管理**: 强制 `pnpm`
- **类型**: 避免 `any`
- **组件**: 函数组件 + Hooks

---

## 套件维护

### 添加新规范到现有插件

1. 确定规范所属插件(如 java-standards)
2. 编辑 `plugins/<plugin>/skills/<plugin>/*.md` 文档
3. 更新 `SKILL.md` (如需要)
4. 更新 `CHANGELOG.md`
5. 测试规范触发

示例:
```bash
# 添加新的 Java 规范
cd plugins/java-standards/skills/java-standards
vim java-coding-standards.md  # 编辑规范
```

### 添加新插件

1. 在 `plugins/` 创建新插件目录
2. 创建插件结构:
   ```
   plugins/new-plugin/
   ├── skills/
   │   └── new-plugin/
   │       └── SKILL.md
   └── commands/ (可选)
   ```
3. 更新 `.claude-plugin/marketplace.json`,添加插件配置
4. 更新 `README.md` 和 `docs/plugins.md`
5. 测试自动触发

### 添加新 Slash Command

1. 确定命令所属插件
2. 在 `plugins/<plugin>/commands/` 创建 `<command-name>.md`
3. 更新 `.claude-plugin/marketplace.json` 中的 commands 数组
4. 更新文档
5. 测试命令执行

示例:
```bash
# 添加新的检查命令
cd plugins/java-standards/commands
touch check-entity.md  # 创建命令文件

# 然后在 marketplace.json 中添加:
# "commands": [..., "./commands/check-entity.md"]
```

### 更新 marketplace.json

每次添加/修改插件时,需要更新 `.claude-plugin/marketplace.json`:

```json
{
  "plugins": [
    {
      "name": "new-plugin",
      "source": "./plugins/new-plugin",
      "description": "新插件描述",
      "version": "1.0.0",
      "skills": ["./skills/new-plugin"],
      "commands": ["./commands/some-command.md"]
    }
  ]
}
```

---

## 版本管理

### 版本号规则

遵循语义化版本 (Semantic Versioning):
- **MAJOR**: 不兼容的 API 变更或架构重构
- **MINOR**: 向后兼容的功能新增
- **PATCH**: 向后兼容的问题修复

当前版本: `2.0.0` (Marketplace 架构)

### 更新检查清单

- [ ] 更新 `.claude-plugin/marketplace.json` 的 metadata.version
- [ ] 更新各插件的 version 字段(如有变更)
- [ ] 更新 `README.md` 版本号
- [ ] 更新 `CHANGELOG.md` 添加版本条目
- [ ] 测试所有 Skills 和 Commands
- [ ] 提交 git commit
- [ ] 打 git tag: `git tag v2.0.0`

---

## 测试指南

### 测试 Skills 自动触发

```
# 测试 java-standards
用户: 帮我创建一个用户 VO 类
预期: 自动加载 java-standards,生成符合规范的 VO

# 测试 frontend
用户: 创建一个 React 组件
预期: 自动加载 frontend,使用函数组件 + Hooks

# 测试 database
用户: 设计一个用户表
预期: 自动加载 database,包含必备字段
```

### 测试 Slash Commands

```bash
# 测试快捷命令
/coding-standards:java
/coding-standards:frontend

# 测试检查命令
/coding-standards:check-vo
/coding-standards:check-naming
/coding-standards:check-logging
/coding-standards:check-mybatis
```

---

## 故障排查

### Skills 未自动触发

**原因**:
- SKILL.md 的 description 不够准确
- 用户请求关键词未匹配

**解决**:
- 优化 SKILL.md 的 description 字段
- 添加更多触发关键词

### Slash Commands 不可用

**原因**:
- commands/ 目录路径错误
- 命令文件命名不规范

**解决**:
- 检查文件路径: `commands/coding-standards:xxx.md`
- 确保命名空间正确: `coding-standards:`

### 规范冲突

**原因**:
- 多个 Skills 包含重叠规范
- 项目级规范与插件规范冲突

**解决**:
- 项目 CLAUDE.md 中明确优先级
- 使用项目级规范覆盖插件规范

---

## 与其他插件集成

### 推荐组合

- **coding-standards** + **code-review**: 规范 + 审查
- **coding-standards** + **testing**: 规范 + 测试生成
- **coding-standards** + **documentation**: 规范 + 文档生成

### 冲突处理

如果与其他插件规范冲突:
1. 在项目 CLAUDE.md 中说明优先级
2. 选择性禁用特定规范
3. 与插件维护者沟通协调

---

## 贡献指南

### 报告问题

在 GitHub Issues 中报告:
- 规范描述不清
- Skills 未触发
- Commands 执行错误
- 规范内容有误

### 贡献规范

1. Fork 仓库
2. 创建分支: `feature/new-standard`
3. 编写规范(含示例)
4. 更新文档
5. 提交 PR

---

## 相关资源

### Claude Code 文档
- [Skills 开发指南](https://docs.claude.com/en/docs/claude-code/skills)
- [Plugin API Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)
- [Slash Commands](https://docs.claude.com/en/docs/claude-code/commands)

### 规范参考
- [阿里巴巴 Java 开发手册](https://github.com/alibaba/p3c)
- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

---

**插件维护者**: kk
**最后更新**: 2025-10-22
**许可证**: MIT
