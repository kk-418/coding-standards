---
name: database
description: MySQL数据库设计规范和MyBatis-Flex ORM使用规范。当设计数据库表、编写SQL、使用MyBatis-Flex、更新数据库记录、配置Service层时使用此Skill。
---

# 数据库设计与 ORM 规范

本 Skill 提供 MySQL 数据库设计和 MyBatis-Flex 使用规范。

## 数据库规范 (database-standards.md)

**强制性规范:**
- **必备字段**: 每张表必须包含 `id`, `create_time`, `update_time`, `is_deleted`
- **金额字段**: 必须使用 `DECIMAL` 类型,禁止 `FLOAT`/`DOUBLE`
- **时间字段**: 使用 `DATETIME` 类型,不用 `TIMESTAMP`
- **查询规范**: 禁止 `SELECT *`,必须明确指定字段

**核心内容:**
- 表设计规范(命名、字段类型、必备字段)
- 索引规范(命名、使用场景)
- SQL 编写规范(查询、更新、JOIN)
- 性能优化规范

## MyBatis-Flex 规范 (mybatis-flex-standards.md)

**适用版本**: MyBatis-Flex 1.11.1+

**强制性规范:**
- **Service 继承**:
  - Service 实现类必须继承 `ServiceImpl<Mapper, Entity>`
  - Service 接口必须继承 `IService<Entity>`
- **更新操作**:
  - ✅ 使用 `UpdateChain` 精确更新指定字段
  - ❌ 禁止用 `updateById` 更新单个/少量字段

**核心内容:**
- 实体类配置(@Table, @Id, 字段映射)
- Mapper 接口规范
- Service 层继承规范
- QueryWrapper 查询构造器
- 分页查询、逻辑删除、乐观锁
- 多数据源配置

## 使用场景

- 设计数据库表结构
- 创建或优化索引
- 编写和优化 SQL 语句
- 配置 MyBatis-Flex 实体类
- 实现 Service 层业务逻辑
- 更新数据库记录
- 复杂查询构造

## 详细文档

请参考同目录下的:
- `database-standards.md` - MySQL 数据库设计规范
- `mybatis-flex-standards.md` - MyBatis-Flex 使用指南

---

**作者**: kk
**版本**: 1.0.0
**最后更新**: 2025-10-22
