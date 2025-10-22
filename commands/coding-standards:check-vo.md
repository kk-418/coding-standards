# VO 类型约束检查

检查 Java VO 类是否符合类型约束规范。

## 检查规则

### ✅ 允许的类型
- `String` - 所有字段必须使用 String 类型

### ❌ 禁止的类型
- `BigDecimal` - 金额/数值字段禁止使用
- `Long` - ID 字段禁止使用
- `LocalDateTime` - 日期时间字段禁止使用
- `LocalDate` - 日期字段禁止使用
- `Date` - 旧版日期类型禁止使用
- `Timestamp` - 时间戳字段禁止使用
- `Integer` - 整数字段禁止使用(除枚举外)
- `Double`, `Float` - 浮点数字段禁止使用

## 检查步骤

1. 读取当前项目中所有以 `VO` 结尾的类文件
2. 分析每个类的字段类型定义
3. 检查是否存在禁止使用的类型
4. 报告违规情况并给出修复建议

## 示例

### ❌ 错误示例
```java
public class UserVO {
    private Long id;              // 错误: 应使用 String
    private BigDecimal amount;    // 错误: 应使用 String
    private LocalDateTime createTime; // 错误: 应使用 String
}
```

### ✅ 正确示例
```java
public class UserVO {
    private String id;            // 正确
    private String amount;        // 正确
    private String createTime;    // 正确
}
```

## 执行检查

请分析当前项目中的所有 VO 类,并报告不符合规范的字段。
