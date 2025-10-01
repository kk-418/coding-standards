# 数据库规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: MySQL数据库设计与使用

---

## 表设计规范

### 表命名规范

- **表名**: 使用小写字母，单词之间用下划线分隔，例如 `user_info`、`order_detail`
- **表名语义**: 表名应该清晰表达表的业务含义，避免使用缩写
- **关联表**: 多对多关系的关联表命名为 `表1_表2`，例如 `user_role`

### 字段定义规范

- **主键**: 表必须有主键，主键字段建议命名为 `id`，类型为 `BIGINT UNSIGNED`，并设置为 `AUTO_INCREMENT`
- **字段命名**: 使用小写字母，单词之间用下划线分隔，例如 `user_name`、`create_time`
- **字段类型选择**:
  - 金额字段使用 `DECIMAL` 类型，不要使用 `FLOAT` 或 `DOUBLE`，避免精度损失
  - 状态字段使用 `TINYINT` 类型存储枚举值
  - 时间字段统一使用 `DATETIME` 类型，不要使用 `TIMESTAMP`(存在2038年问题)
  - 字符串字段长度合理设置，避免过长导致索引失效
- **NOT NULL约束**: 字段尽可能定义为 `NOT NULL`，并设置默认值
- **字段注释**: 所有字段必须添加注释说明字段用途

### 必备字段

每张表必须包含以下字段:

```sql
CREATE TABLE table_name (
    id BIGINT UNSIGNED AUTO_INCREMENT COMMENT '主键ID',
    create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    is_deleted TINYINT NOT NULL DEFAULT 0 COMMENT '逻辑删除标识(0:未删除,1:已删除)',
    PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='表注释';
```

## 索引规范

### 索引命名规范

- **普通索引**: `idx_字段名`，例如 `idx_user_name`
- **唯一索引**: `uk_字段名`，例如 `uk_mobile`
- **联合索引**: `idx_字段1_字段2`，例如 `idx_user_id_status`

### 索引使用规范

- **索引数量**: 单表索引数量不超过5个，单个索引的字段数不超过5个
- **最左前缀**: 联合索引遵循最左前缀原则，将区分度高的字段放在前面
- **覆盖索引**: 优先考虑使用覆盖索引，避免回表查询
- **索引失效场景**: 避免以下导致索引失效的情况
  - 在索引列上使用函数或计算
  - 使用 `!=`、`<>` 操作符
  - 使用 `LIKE` 以通配符开头，例如 `LIKE '%abc'`
  - 使用 `OR` 连接条件，除非所有条件列都有索引
  - 隐式类型转换

## SQL编写规范

### 查询规范

- **SELECT语句**: 不要使用 `SELECT *`，应该明确指定需要查询的字段
- **分页查询**: 使用 `LIMIT` 进行分页时，必须指定排序规则，避免数据重复或遗漏
  ```sql
  -- 推荐
  SELECT id, user_name FROM user ORDER BY id LIMIT 10, 20;

  -- 不推荐
  SELECT * FROM user LIMIT 10, 20;
  ```
- **COUNT查询**: `COUNT(*)` 和 `COUNT(1)` 性能相同，推荐使用 `COUNT(*)`
- **IN条件**: `IN` 操作的参数个数建议不超过500个，超过时应该分批查询
- **子查询**: 避免使用复杂的子查询，可以考虑改写为 `JOIN` 查询

### 更新规范

- **批量操作**: 批量插入使用 `INSERT INTO ... VALUES (...), (...), (...)` 语法，避免循环单条插入
- **UPDATE/DELETE**: 必须带上 `WHERE` 条件，避免全表更新或删除
- **事务控制**: 涉及多表操作时必须使用事务，并合理设置事务隔离级别
- **乐观锁**: 更新操作使用乐观锁机制，通过版本号字段控制并发

### JOIN规范

- **JOIN类型**: 优先使用 `INNER JOIN`，避免使用 `LEFT JOIN` 后再通过 `WHERE` 条件过滤右表数据
- **JOIN数量**: 一条SQL语句的 `JOIN` 表数量不超过3个
- **JOIN条件**: 必须在 `ON` 子句中指定关联条件，不要放在 `WHERE` 子句中

## 性能优化规范

### 查询优化

- **EXPLAIN分析**: 上线前必须使用 `EXPLAIN` 分析SQL执行计划，确保使用了正确的索引
- **慢查询监控**: 开启慢查询日志，定期分析慢查询并优化
- **避免全表扫描**: 确保查询条件列有索引，避免全表扫描
- **分页优化**: 深分页场景使用延迟关联或子查询优化
  ```sql
  -- 深分页优化
  SELECT a.* FROM user a
  INNER JOIN (SELECT id FROM user ORDER BY id LIMIT 100000, 20) b
  ON a.id = b.id;
  ```

### 表结构优化

- **垂直拆分**: 字段过多的表考虑垂直拆分，将不常用字段拆分到扩展表
- **水平拆分**: 数据量超过500万的表考虑水平分表
- **冷热数据分离**: 历史数据归档到历史表，保持主表数据量在合理范围

## 参考资料

本规范主要参考以下文档:
- 《阿里巴巴Java开发手册(嵩山版)》MySQL数据库规约章节
