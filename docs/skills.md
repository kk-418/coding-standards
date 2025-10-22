# Skills 总览

本文档提供 coding-standards 套件中所有 Skills 的总览和索引。

---

## Skills 组织结构

coding-standards 套件包含 **6 个插件**,每个插件提供 **1 个 Skill**,共计 **6 个 Skills**。

每个 Skill 包含多个规范文档,总计 **18 个规范文档**。

---

## Skill 列表

### 1. work-guidelines

**路径**: `plugins/work-guidelines/skills/work-guidelines/`

**描述**: 工作规范与调试方法论

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `guidelines.md` - 工作流程规范
- `values.md` - 七大代码质量原则
- `debug.md` - 调试方法论

**触发条件**:
- 开始新项目或任务
- 需要调试复杂问题
- 查询代码质量标准

---

### 2. java-standards

**路径**: `plugins/java-standards/skills/java-standards/`

**描述**: Java/Spring Boot 编码规范

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `java-coding-standards.md` - 完整 Java 编码规范
- `java21-standards.md` - Java 21 新特性使用指南
- `java-test-standards.md` - 测试编写和最佳实践

**触发条件**:
- 创建 Java 类(Entity/VO/Service等)
- 使用 Lombok 或 Record
- 编写 Spring Boot 代码
- 编写 Java 测试

---

### 3. build-tools

**路径**: `plugins/build-tools/skills/build-tools/`

**描述**: Gradle/Maven 构建工具规范

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `gradle-standards.md` - Gradle 9.0+ 构建规范
- `maven-standards.md` - Maven 3.9+ 构建规范

**触发条件**:
- 配置 Gradle 或 Maven
- 管理项目依赖
- 设置编译选项

---

### 4. database

**路径**: `plugins/database/skills/database/`

**描述**: MySQL + MyBatis-Flex 数据库规范

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `database-standards.md` - MySQL 数据库设计规范
- `mybatis-flex-standards.md` - MyBatis-Flex 使用规范

**触发条件**:
- 设计数据库表
- 使用 MyBatis-Flex
- 编写 SQL 语句
- 更新数据库记录

---

### 5. frontend

**路径**: `plugins/frontend/skills/frontend/`

**描述**: React/Vue + TypeScript 前端开发规范

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `frontend-standards.md` - 完整前端开发规范

**触发条件**:
- 开发 React/Vue 项目
- 配置 TypeScript
- 创建前端组件

---

### 6. common

**路径**: `plugins/common/skills/common/`

**描述**: 通用编码规范

**包含文档**:
- `SKILL.md` - Skill 定义和概览
- `common-coding.md` - 跨语言通用编码规范

**触发条件**:
- 需要跨语言编码标准
- 命名规范查询
- 异常处理指导

---

## 规范文档统计

| 插件 | Skill 数 | 规范文档数 | 总行数(估算) |
|------|----------|------------|--------------|
| work-guidelines | 1 | 4 | ~800 |
| java-standards | 1 | 4 | ~1500 |
| build-tools | 1 | 3 | ~1000 |
| database | 1 | 3 | ~1000 |
| frontend | 1 | 2 | ~800 |
| common | 1 | 2 | ~1000 |
| **总计** | **6** | **18** | **~6100** |

---

## Skills 自动触发机制

Claude Code 会根据以下条件自动加载相关 Skills:

### 关键词触发

| 关键词 | 触发 Skill |
|--------|-----------|
| "工作规范", "调试", "质量原则" | work-guidelines |
| "Java", "Spring Boot", "VO", "Entity" | java-standards |
| "Gradle", "Maven", "构建", "依赖" | build-tools |
| "数据库", "表", "MyBatis", "SQL" | database |
| "React", "Vue", "前端", "组件", "TypeScript" | frontend |
| "命名", "集合", "异常处理" | common |

### 文件类型触发

| 文件扩展名 | 触发 Skill |
|-----------|-----------|
| `.java` | java-standards |
| `.gradle`, `.gradle.kts`, `pom.xml` | build-tools |
| `.xml` (MyBatis Mapper) | database |
| `.tsx`, `.jsx`, `.vue` | frontend |

### 操作触发

| 操作 | 触发 Skill |
|------|-----------|
| 创建 VO/DTO/Entity 类 | java-standards |
| 设计数据库表 | database |
| 创建 React/Vue 组件 | frontend |
| 配置构建文件 | build-tools |

---

## Skills 使用示例

### 示例 1: 自动触发

```
用户: 帮我创建一个用户 VO 类

Claude: (自动加载 java-standards Skill)
我会创建一个符合规范的 UserVO 类:
- 所有字段使用 String 类型(符合 VO 类型约束)
- 类名以 VO 结尾
- 使用 Lombok 注解
...
```

### 示例 2: 手动加载

```
用户: /coding-standards:java

Claude: (手动加载 java-standards Skill)
已加载 Java 编码规范。以下是核心规范:
1. VO 类型约束: 所有字段使用 String
2. 日志使用: 使用 @Slf4j,禁止 System.out.println
...
```

### 示例 3: 多 Skills 同时加载

```
用户: 创建一个用户服务,包括数据库表和 Service 类

Claude: (同时加载 database 和 java-standards Skills)
我会同时设计数据库表和创建 Service 类:

1. 数据库表设计(database Skill):
   - 包含必备字段: id, create_time, update_time, is_deleted
   - 使用 BIGINT UNSIGNED 作为主键

2. Service 类创建(java-standards Skill):
   - Service 继承 ServiceImpl
   - 使用 @Slf4j 进行日志记录
...
```

---

## Skills 优先级

当多个 Skills 规范冲突时,按以下优先级处理:

1. **项目级规范** (`.claude/CLAUDE.md`) - 最高优先级
2. **特定技术栈 Skill** (java-standards, frontend 等)
3. **通用规范 Skill** (common, work-guidelines)
4. **Claude 默认行为** - 最低优先级

---

## Skills 定制

### 在项目中覆盖 Skill 规范

在项目的 `.claude/CLAUDE.md` 中添加:

```markdown
# 项目特定规范

本项目在 coding-standards 插件基础上,做以下调整:

## Java 规范覆盖
- VO 类型约束: 允许使用 LocalDateTime(覆盖原规范)
- 其他规范保持不变

## 数据库规范覆盖
- 允许使用雪花算法生成 ID(覆盖原规范)
```

### 禁用特定 Skill

暂不支持完全禁用 Skill,但可以在项目配置中明确说明不遵循某些规范。

---

## 相关资源

- [插件目录](./plugins.md) - 查看所有插件详情
- [使用指南](./usage.md) - 安装和使用教程
- [GitHub 仓库](https://github.com/kk-418/coding-standards) - 源代码和问题反馈

---

**作者**: kk
**版本**: 2.0.0
**最后更新**: 2025-10-22
