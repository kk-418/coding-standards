# 命名规范检查

检查代码是否符合命名规范。

## 检查规则

### 1. 类命名规范
- **格式**: PascalCase (大驼峰)
- **实体类**: 无特殊后缀
- **VO 类**: 以 `VO` 结尾
- **DTO 类**: 以 `DTO` 结尾
- **枚举类**: 以 `Enum` 结尾
- **Service 实现**: 以 `ServiceImpl` 结尾
- **Controller**: 以 `Controller` 结尾

### 2. 方法命名规范
- **格式**: camelCase (小驼峰)
- **布尔返回**: 以 `is`, `has`, `can`, `should` 开头
- **获取方法**: 以 `get` 开头
- **设置方法**: 以 `set` 开头
- **查询方法**: 以 `query`, `find`, `list` 开头
- **保存方法**: 以 `save`, `add`, `create` 开头
- **更新方法**: 以 `update`, `modify` 开头
- **删除方法**: 以 `delete`, `remove` 开头

### 3. 变量命名规范
- **格式**: camelCase (小驼峰)
- **集合变量**: 使用复数形式(users, orders)
- **布尔变量**: 以 `is`, `has`, `can` 开头
- **避免**: 单字符变量名(除循环索引)

### 4. 常量命名规范
- **格式**: UPPER_SNAKE_CASE (全大写下划线)
- **位置**: 定义在常量类或枚举中
- **禁止**: 魔法值,所有常量必须命名

### 5. 包命名规范
- **格式**: 全小写
- **分隔**: 使用点号分隔
- **结构**: com.company.project.module

### 6. 测试方法命名规范
- **格式**: `should_<预期结果>_when_<测试条件>`
- **示例**: `should_returnUser_when_userExists`

## 检查步骤

1. 扫描指定的代码文件或目录
2. 分析类名、方法名、变量名、常量名
3. 检查是否符合命名规范
4. 报告违规情况并给出修复建议

## 常见问题

### ❌ 错误示例
```java
// 类名不规范
public class paymentStatus {}  // 应为 PaymentStatus

// 枚举类缺少后缀
public enum PaymentStatus {}    // 应为 PaymentStatusEnum

// 方法名不规范
public void GetUser() {}        // 应为 getUser

// 常量名不规范
public static final String apiUrl = "...";  // 应为 API_URL

// 变量名不规范
List<User> UserList;           // 应为 userList 或 users
```

### ✅ 正确示例
```java
public class PaymentStatusEnum {}
public void getUser() {}
public static final String API_URL = "...";
List<User> users;
```

## 执行检查

请分析指定的代码文件,并报告不符合命名规范的项目。
