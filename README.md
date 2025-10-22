# 编码规范插件 (Coding Standards Plugin)

> **作者**: kk
> **版本**: 1.0.0
> **许可**: MIT

---

## 📖 简介

这是一个为 Claude Code 设计的中文编码规范插件,提供 Java/Spring Boot、前端、数据库等技术栈的编码标准和最佳实践。

**特点:**
- 🎯 **自动触发**: Claude 根据上下文自动加载相关规范
- ⚡ **快捷命令**: 通过 slash commands 快速访问规范
- 🔍 **代码检查**: 内置 4 个检查命令验证代码质量
- 📚 **模块化**: 按技术栈清晰组织,易于维护
- 🇨🇳 **中文友好**: 全中文文档,适合中文开发团队

---

## 🚀 快速开始

### 安装插件

```bash
# 方式1: 克隆到本地插件目录
git clone https://github.com/kk-418/coding-standards.git ~/.claude/plugins/coding-standards

# 方式2: 在 Claude Code 中安装
/plugin install coding-standards
```

### 验证安装

安装成功后,以下 Skills 将自动可用:
- `work-guidelines` - 工作规范与调试方法论
- `java-standards` - Java/Spring Boot 编码规范
- `build-tools` - Gradle/Maven 构建规范
- `database` - 数据库设计与 MyBatis-Flex 规范
- `frontend` - 前端开发规范(React/Vue + TypeScript)
- `common` - 通用编码规范(跨语言)

---

## 📚 Skills 说明

### 1. work-guidelines
**工作规范与调试方法论**

自动触发场景:
- 开始新项目或任务
- 需要调试复杂问题
- 需要了解代码质量标准

核心内容:
- 工作流程规范(作者信息、输出语言等)
- 七大代码质量原则
- **禁止猜测式调试**,必须基于证据

### 2. java-standards
**Java/Spring Boot 编码规范**

自动触发场景:
- 创建 Java 类(Entity/VO/Service等)
- 使用 Spring Boot 框架
- 编写 Java 测试

强制性规范:
- VO 类型约束(所有字段用 String)
- 日志使用(禁止 System.out.println)
- Result 使用(分页不嵌套)
- 命名规范(Enum 后缀等)

### 3. build-tools
**Gradle/Maven 构建规范**

自动触发场景:
- 配置项目构建
- 管理依赖
- 设置编译选项

核心内容:
- Gradle 9.0+ 配置
- Maven 3.9+ 配置
- 依赖管理最佳实践

### 4. database
**数据库设计与 MyBatis-Flex 规范**

自动触发场景:
- 设计数据库表
- 使用 MyBatis-Flex
- 更新数据库记录

强制性规范:
- 必备字段(id, create_time等)
- Service 层必须继承 ServiceImpl
- 更新操作使用 UpdateChain

### 5. frontend
**前端开发规范**

自动触发场景:
- 开发 React/Vue 项目
- 配置 TypeScript
- 创建前端组件

强制性规范:
- 强制使用 pnpm
- 避免 any 类型
- 使用函数组件 + Hooks

### 6. common
**通用编码规范**

自动触发场景:
- 需要跨语言编码标准
- 命名规范参考
- 异常处理指导

核心内容:
- 集合处理(判空等)
- 命名规范(类/方法/变量/常量)
- 日期时间处理
- 并发处理
- 异常处理

---

## 🎯 Slash Commands

### 快捷命令

```bash
# 快速加载 Java 编码规范
/coding-standards:java

# 快速加载前端开发规范
/coding-standards:frontend
```

### 检查命令

```bash
# 检查 VO 类型约束
/coding-standards:check-vo

# 检查命名规范
/coding-standards:check-naming

# 检查日志使用
/coding-standards:check-logging

# 检查 MyBatis-Flex 规范
/coding-standards:check-mybatis
```

---

## 💡 使用示例

### 示例 1: 自动触发 Skill

```
用户: 帮我创建一个用户 VO 类

Claude: (自动加载 java-standards Skill)
我会创建一个符合规范的 UserVO 类:
- 所有字段使用 String 类型(符合 VO 类型约束)
- 类名以 VO 结尾
- 使用 Lombok 注解
...
```

### 示例 2: 使用快捷命令

```
用户: /coding-standards:java

Claude: 已加载 Java 编码规范。以下是核心规范:
1. VO 类型约束: 所有字段使用 String
2. 日志使用: 使用 @Slf4j,禁止 System.out.println
...
```

### 示例 3: 代码检查

```
用户: /coding-standards:check-vo

Claude: 开始检查 VO 类型约束...

发现 2 处违规:
1. UserVO.java:12 - id 字段使用了 Long 类型,应改为 String
2. OrderVO.java:15 - amount 字段使用了 BigDecimal,应改为 String

建议修复...
```

---

## ⚠️ 强制性规范速查

### 通用规范
- **Git 操作**: 文件移动/重命名必须使用 `git mv`
- **集合判空**: 使用 `CollectionUtils.isEmpty()`
- **魔法值**: 禁止,必须定义为常量
- **线程池**: 使用 `ThreadPoolExecutor`,不用 `Executors`
- **调试方法**: 禁止猜测,必须基于证据

### Java 规范
- **VO 类型**: 所有字段用 `String`,禁止 `BigDecimal`/`Long`/`LocalDateTime`
- **日志使用**: 用 `@Slf4j`,禁止 `System.out.println`
- **Result**: 分页直接返回 `PageResult<T>`,不嵌套
- **枚举命名**: 以 `Enum` 结尾
- **测试 Mock**: 用 `@MockitoBean`,禁止 `@MockBean`

### 数据库规范
- **必备字段**: `id`, `create_time`, `update_time`, `is_deleted`
- **金额字段**: 用 `DECIMAL`,禁止 `FLOAT`/`DOUBLE`
- **查询规范**: 禁止 `SELECT *`
- **Service 继承**: 必须继承 `ServiceImpl`
- **更新操作**: 用 `UpdateChain`,避免 `updateById`

### 前端规范
- **包管理器**: 强制 `pnpm`,禁止 `npm`/`yarn`
- **类型安全**: 避免 `any` 类型
- **组件规范**: 函数组件 + Hooks

---

## 📁 项目结构

```
coding-standards/
├── .claude-plugin/
│   └── plugin.json              # 插件配置
├── skills/                       # Skills 目录
│   ├── work-guidelines/         # 工作规范 Skill
│   ├── java-standards/          # Java 规范 Skill
│   ├── build-tools/             # 构建工具 Skill
│   ├── database/                # 数据库 Skill
│   ├── frontend/                # 前端 Skill
│   └── common/                  # 通用规范 Skill
├── commands/                    # Slash Commands
│   ├── coding-standards:java.md
│   ├── coding-standards:frontend.md
│   ├── coding-standards:check-vo.md
│   ├── coding-standards:check-naming.md
│   ├── coding-standards:check-logging.md
│   └── coding-standards:check-mybatis.md
├── README.md                    # 本文件
├── CHANGELOG.md                 # 版本日志
├── LICENSE                      # MIT 许可证
└── CLAUDE.md                    # 插件开发文档
```

---

## 🔧 配置与自定义

### 在项目中使用

在项目的 `.claude/CLAUDE.md` 中引用:

```markdown
# CLAUDE Entry

本项目使用 coding-standards 插件提供的编码规范。

请严格遵守以下规范:
- Java 编码规范(自动加载)
- 数据库设计规范(自动加载)
- 前端开发规范(自动加载)
```

### 自定义规范

如需为特定项目添加自定义规范:

1. 在项目 `.claude/` 目录添加自定义 skills
2. 自定义规范优先于插件规范
3. 保持与插件规范的一致性

---

## 📝 规范来源

- Java 编码规范基于《阿里巴巴 Java 开发规范(嵩山版)》整理优化
- 前端规范参考 Airbnb JavaScript Style Guide
- 数据库规范基于 MySQL 官方最佳实践

---

## 🤝 贡献指南

欢迎贡献新的编码规范或改进现有规范:

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/new-standard`)
3. 提交更改 (`git commit -m '添加新规范'`)
4. 推送到分支 (`git push origin feature/new-standard`)
5. 创建 Pull Request

### 贡献规范

- 遵循现有文档格式
- 包含正确示例和错误示例
- 更新 CHANGELOG.md
- 说明规范的适用场景

---

## 📞 反馈与支持

### 问题反馈

如果发现问题或有改进建议:
- 提交 Issue: https://github.com/kk-418/coding-standards/issues
- 联系维护者: kk

### 常见问题

**Q: 如何禁用某个 Skill?**
A: 暂不支持禁用单个 Skill,但可以在项目 CLAUDE.md 中覆盖特定规范。

**Q: 可以自定义检查命令吗?**
A: 可以,在项目 `.claude/commands/` 目录添加自定义命令。

**Q: 规范与公司内部规范冲突怎么办?**
A: 项目级规范优先于插件规范,在项目 CLAUDE.md 中说明差异即可。

---

## 📄 许可证

本项目基于 [MIT License](LICENSE) 开源。

---

## 🔗 相关资源

### 官方文档
- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [MyBatis-Flex 官方文档](https://mybatis-flex.com/)
- [React 官方文档](https://react.dev/)
- [Vue 官方文档](https://vuejs.org/)

### Claude Code
- [Claude Code 文档](https://docs.claude.com/en/docs/claude-code)
- [Skills 开发指南](https://docs.claude.com/en/docs/claude-code/skills)
- [Plugin 开发指南](https://docs.claude.com/en/docs/claude-code/plugins-reference)

---

**最后更新**: 2025-10-22
**维护者**: kk
