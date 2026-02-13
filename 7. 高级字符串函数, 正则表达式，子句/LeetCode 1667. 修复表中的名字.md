# 题目概述

**题目描述**：  
表 `Users` 包含两列：

- `user_id` (用户ID，主键，int)
- `name` (用户名，varchar)

要求编写一个 SQL 查询来修复名字，使得它们只有**第一个字符是大写**，**其余字符都是小写**。  
返回的结果表需要按照 `user_id` 进行排序。

**示例**：  
输入：

|   |   |
|---|---|
|user_id|name|
|1|aLice|
|2|bOB|

输出：

|   |   |
|---|---|
|user_id|name|
|1|Alice|
|2|Bob|

---

# 解题思路

这就好比在 Java 中处理字符串一样，要把 `"aLice"` 变成 `"Alice"`，我们需要做三步操作：

1. **取首字母**：拿到 `"a"`，并强制转为**大写** (`A`)。
2. **取剩余部分**：拿到 `"Lice"`，并强制转为**小写** (`lice`)。
3. **拼接**：将两部分拼起来 (`Alice`)。

在 SQL 中，我们需要用到对应的字符串函数：

- **截取子串**：`SUBSTRING(string, start, length)` 或 `LEFT/RIGHT`。注意 SQL 中索引通常从 **1** 开始。
- **大小写转换**：`UPPER()` 转大写，`LOWER()` 转小写。
- **拼接**：`CONCAT()`。

**具体逻辑**：

1. `UPPER(LEFT(name, 1))`：取第 1 个字符转大写。
2. `LOWER(SUBSTRING(name, 2))`：从第 2 个字符开始截取到末尾，转小写。
3. `CONCAT(..., ...)`：拼在一起。

---

# SQL 代码

```sql
# Write your MySQL query statement below
select
    user_id,
    concat(
        upper(substring(name, 1, 1))
        ,
        lower(substring(name, 2))
    ) as name
from 
    Users
order by 
    user_id;
```

### 知识点扩展 (Pair Programming Tips)

- **SUBSTRING 的变体**:

- `SUBSTRING(str, pos)`: 从 pos 开始截取到最后。
- `SUBSTRING(str, pos, len)`: 从 pos 开始截取 len 个字符。
- 在 MySQL 中 `SUBSTR()` 和 `SUBSTRING()` 是同义词。

- **不同数据库的差异**:

- **MySQL/PostgreSQL**: 支持 `CONCAT`, `UPPER`, `LOWER`, `SUBSTRING`

concat(upper(left(name, 1)))