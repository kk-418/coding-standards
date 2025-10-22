# 使用指南

本文档提供 coding-standards 套件的完整安装和使用教程。

---

## 快速开始

### 前置要求

- Claude Code CLI (最新版本)
- Git (用于克隆仓库)

### 安装步骤

#### 方式 1: 通过 Git 克隆(推荐)

```bash
# 克隆到 Claude Code 插件目录
git clone https://github.com/kk-418/coding-standards.git ~/.claude/plugins/coding-standards

# 验证安装
ls ~/.claude/plugins/coding-standards
```

#### 方式 2: 通过插件市场

```bash
# 添加插件市场
/plugin marketplace add kk-418/coding-standards

# 安装插件套件
/plugin install coding-standards
```

### 验证安装

安装成功后,Skills 会自动可用。测试方法:

```bash
# 启动 Claude Code
claude

# 测试 Skills 自动触发
> 帮我创建一个用户 VO 类

# Claude 应该自动加载 java-standards Skill 并遵循规范
```

---

## 插件架构

### Marketplace 模式

coding-standards 使用 Marketplace 模式组织多个插件:

```
coding-standards/                    # 根目录
├── .claude-plugin/
│   └── marketplace.json            # 插件市场配置
├── plugins/                         # 插件目录
│   ├── work-guidelines/            # 插件 1
│   ├── java-standards/             # 插件 2
│   ├── build-tools/                # 插件 3
│   ├── database/                   # 插件 4
│   ├── frontend/                   # 插件 5
│   └── common/                     # 插件 6
└── docs/                            # 文档
```

### 插件组成

每个插件包含:
- **skills/** - Skills 定义和规范文档
- **commands/** - Slash Commands (可选)
- **SKILL.md** - Skill 元数据和描述

---

## Skills 使用

### 自动触发

Claude Code 会根据上下文自动加载相关 Skills,无需手动操作。

**示例 1: 创建 Java 类**

```
用户: 创建一个 UserVO 类,包含用户基本信息

Claude: (自动加载 java-standards)
我会创建符合规范的 UserVO 类:

@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserVO {
    private String id;           // 注意:使用 String 而非 Long
    private String username;
    private String email;
    private String createTime;   // 注意:使用 String 而非 LocalDateTime
}
```

**示例 2: 设计数据库表**

```
用户: 设计一个用户表

Claude: (自动加载 database)
我会设计符合规范的用户表:

CREATE TABLE user (
    id BIGINT UNSIGNED PRIMARY KEY COMMENT '主键ID',
    username VARCHAR(50) NOT NULL COMMENT '用户名',
    email VARCHAR(100) COMMENT '邮箱',
    create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    is_deleted TINYINT NOT NULL DEFAULT 0 COMMENT '删除标记'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

### 触发条件

查看 [Skills 总览](./skills.md) 了解每个 Skill 的详细触发条件。

---

## Slash Commands 使用

### 快捷命令

快速加载特定规范:

```bash
# 加载 Java 编码规范
/coding-standards:java

# 加载前端开发规范
/coding-standards:frontend
```

### 检查命令

对代码进行规范检查:

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

### 检查命令示例

```
用户: /coding-standards:check-vo

Claude: 开始检查 VO 类型约束...

发现 2 处违规:
1. src/main/java/com/example/vo/UserVO.java:12
   - 问题: id 字段使用了 Long 类型
   - 建议: 改为 String 类型

2. src/main/java/com/example/vo/OrderVO.java:15
   - 问题: amount 字段使用了 BigDecimal 类型
   - 建议: 改为 String 类型

是否需要我帮您修复这些问题?
```

---

## 在项目中使用

### 项目级配置

在项目根目录创建 `.claude/CLAUDE.md`:

```markdown
# CLAUDE Entry

本项目使用 coding-standards 插件套件提供的编码规范。

## 使用的插件

- work-guidelines: 工作规范(所有开发者必读)
- java-standards: Java 编码规范
- database: 数据库设计规范
- common: 通用编码规范

## 项目特定规范

### Java 规范调整
- VO 类型约束: 允许 LocalDateTime(覆盖原规范)
- 其他规范保持不变

### 数据库规范调整
- 主键生成: 使用雪花算法(覆盖原规范)
```

### 全局配置

在用户全局配置 `~/.claude/CLAUDE.md` 中引用:

```markdown
# CLAUDE Entry

@coding-standards/docs/plugins.md

## 全局规则
- 所有输出使用简体中文
- 作者信息统一使用 "kk"
```

---

## 按需加载插件

如果只需要特定插件,可以在项目配置中明确:

```markdown
# CLAUDE Entry

## 仅使用以下插件

- java-standards: Java 编码规范
- database: 数据库规范

注意:不使用 frontend 和 build-tools 插件
```

---

## 规范冲突处理

### 优先级顺序

1. **项目级规范** (`.claude/CLAUDE.md`) - 最高优先级
2. **插件规范** (Skills 提供)
3. **Claude 默认行为** - 最低优先级

### 覆盖示例

如果项目需要覆盖某些规范:

```markdown
# 项目 CLAUDE.md

本项目基于 coding-standards,但做以下调整:

## VO 类型约束覆盖
- 允许使用 LocalDateTime 和 LocalDate
- 理由:与前端约定直接传输日期对象

## 其他规范
- 保持 coding-standards 的所有其他规范
```

---

## 团队协作

### 团队规范同步

1. 将 coding-standards 加入项目依赖
2. 在 `.claude/CLAUDE.md` 中明确使用的规范
3. 提交到版本控制系统

```bash
# 项目结构
your-project/
├── .claude/
│   └── CLAUDE.md           # 引用 coding-standards
├── src/
└── README.md
```

### 新成员入职

新成员只需:
1. 安装 Claude Code
2. 克隆项目
3. Claude Code 自动加载项目配置和规范

---

## 更新插件

### 检查更新

```bash
# 查看当前版本
cat ~/.claude/plugins/coding-standards/.claude-plugin/marketplace.json | grep version

# 查看远程最新版本
git -C ~/.claude/plugins/coding-standards fetch
git -C ~/.claude/plugins/coding-standards log --oneline origin/main
```

### 执行更新

```bash
# 拉取最新版本
cd ~/.claude/plugins/coding-standards
git pull origin main

# 查看更新日志
cat CHANGELOG.md
```

---

## 故障排查

### Skills 未自动触发

**症状**: 创建 Java 类时,Claude 没有遵循规范

**排查步骤**:

1. 确认插件已安装:
   ```bash
   ls ~/.claude/plugins/coding-standards
   ```

2. 检查 SKILL.md 的 description:
   ```bash
   cat ~/.claude/plugins/coding-standards/plugins/java-standards/skills/java-standards/SKILL.md
   ```

3. 尝试手动加载:
   ```
   /coding-standards:java
   ```

### Slash Commands 不可用

**症状**: 输入 `/coding-standards:java` 没有反应

**排查步骤**:

1. 确认命令文件存在:
   ```bash
   ls ~/.claude/plugins/coding-standards/plugins/java-standards/commands/
   ```

2. 检查 marketplace.json 配置:
   ```bash
   cat ~/.claude/plugins/coding-standards/.claude-plugin/marketplace.json
   ```

### 规范冲突

**症状**: 多个插件给出不同的建议

**解决方案**:
在项目 `.claude/CLAUDE.md` 中明确优先级:

```markdown
## 规范优先级

1. 项目特定规范(本文件)
2. java-standards
3. common
```

---

## 最佳实践

### 1. 分层使用规范

```
work-guidelines (所有项目)
    ↓
common (跨语言通用)
    ↓
特定技术栈规范 (java-standards/frontend/database)
```

### 2. 逐步引入规范

不要一次性引入所有规范,建议顺序:

1. **第一周**: work-guidelines + common
2. **第二周**: 添加主技术栈规范(如 java-standards)
3. **第三周**: 添加辅助规范(如 database, build-tools)

### 3. 定期审查

每个迭代结束后:
- 运行检查命令
- 审查是否遵循规范
- 更新项目特定规范

---

## 常见问题

### Q: 如何禁用某个 Skill?

A: 暂不支持完全禁用,但可以在项目配置中明确不使用:

```markdown
注意:本项目不使用 frontend 规范
```

### Q: 可以自定义检查命令吗?

A: 可以,在项目 `.claude/commands/` 添加自定义命令:

```bash
mkdir -p .claude/commands
touch .claude/commands/check-custom.md
```

### Q: 规范与公司内部标准冲突?

A: 项目级规范优先,在 `.claude/CLAUDE.md` 中覆盖:

```markdown
## 公司标准覆盖
- VO 允许使用 Long 类型
- 理由:与公司内部框架约定一致
```

---

## 相关资源

- [插件目录](./plugins.md) - 查看所有插件详情
- [Skills 总览](./skills.md) - 查看所有 Skills 和触发条件
- [GitHub 仓库](https://github.com/kk-418/coding-standards) - 源代码和问题反馈
- [更新日志](../CHANGELOG.md) - 版本历史

---

## 反馈与贡献

### 问题反馈

如果发现问题或有改进建议:
- 提交 Issue: https://github.com/kk-418/coding-standards/issues
- 联系维护者: kk

### 贡献规范

欢迎贡献新的编码规范:

1. Fork 仓库
2. 创建分支: `git checkout -b feature/new-standard`
3. 编写规范(含示例)
4. 更新文档
5. 提交 PR

---

**作者**: kk
**版本**: 2.0.0
**最后更新**: 2025-10-22
