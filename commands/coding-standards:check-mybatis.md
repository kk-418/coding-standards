# MyBatis-Flex 规范检查

检查 MyBatis-Flex 代码是否符合使用规范。

## 检查规则

### 1. Service 层继承规范(强制)

**Service 接口必须继承 `IService<Entity>`:**
```java
public interface UserService extends IService<User> {
    // 自定义业务方法
}
```

**Service 实现类必须继承 `ServiceImpl<Mapper, Entity>`:**
```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
    // 实现业务逻辑
}
```

### 2. 更新操作规范(强制)

**❌ 禁止使用 `updateById` 更新单个或少量字段:**
```java
// 错误: 仅更新一个字段却加载整个对象
User user = new User();
user.setId(userId);
user.setStatus("ACTIVE");
userMapper.updateById(user);  // 禁止
```

**✅ 必须使用 `UpdateChain` 精确更新指定字段:**
```java
// 正确: 精确更新指定字段
UpdateChain.of(User.class)
    .set(User::getStatus, "ACTIVE")
    .where(User::getId).eq(userId)
    .update();
```

**例外情况**(可使用 `updateById`):
- 表单编辑: 用户提交表单需要更新大部分字段
- 数据同步: 从外部系统同步数据需要更新整个对象

### 3. 实体类配置规范

**必须配置表名:**
```java
@Table("tb_user")
public class User {
    @Id(keyType = KeyType.Auto)
    private Long id;
    // ...
}
```

**字段映射:**
- 使用 `@Column` 指定数据库字段名(如有差异)
- 逻辑删除字段使用 `@Column(isLogicDelete = true)`
- 乐观锁字段使用 `@Column(version = true)`

### 4. QueryWrapper 使用规范

**推荐使用 Lambda 方式:**
```java
// 推荐
QueryWrapper query = QueryWrapper.create()
    .select(User::getId, User::getName)
    .from(User.class)
    .where(User::getStatus).eq("ACTIVE")
    .and(User::getAge).ge(18);
```

## 检查步骤

1. **检查 Service 继承**:
   - 扫描所有 Service 接口,检查是否继承 `IService`
   - 扫描所有 ServiceImpl 类,检查是否继承 `ServiceImpl`

2. **检查更新操作**:
   - 查找 `updateById` 方法调用
   - 分析更新场景是否适合使用 `updateById`
   - 建议改用 `UpdateChain` 的位置

3. **检查实体类配置**:
   - 检查 `@Table` 注解配置
   - 检查主键 `@Id` 配置
   - 检查逻辑删除和乐观锁字段

4. **检查 QueryWrapper**:
   - 推荐使用 Lambda 方式而非字符串字段名

## 常见问题示例

### ❌ 错误示例

```java
// 1. Service 未继承
public class UserService {  // 错误: 应继承 IService<User>
}

// 2. 错误的更新方式
public void updateUserStatus(Long userId, String status) {
    User user = new User();
    user.setId(userId);
    user.setStatus(status);
    userMapper.updateById(user);  // 错误: 应使用 UpdateChain
}

// 3. 实体类缺少配置
public class User {  // 错误: 缺少 @Table 注解
    private Long id;
}
```

### ✅ 正确示例

```java
// 1. Service 正确继承
public interface UserService extends IService<User> {
}

@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User>
    implements UserService {
}

// 2. 正确的更新方式
public void updateUserStatus(Long userId, String status) {
    UpdateChain.of(User.class)
        .set(User::getStatus, status)
        .set(User::getUpdateTime, LocalDateTime.now())
        .where(User::getId).eq(userId)
        .update();
}

// 3. 实体类正确配置
@Table("tb_user")
public class User {
    @Id(keyType = KeyType.Auto)
    private Long id;
}
```

## 执行检查

请分析当前项目中的 MyBatis-Flex 相关代码,并报告不符合规范的使用情况。
