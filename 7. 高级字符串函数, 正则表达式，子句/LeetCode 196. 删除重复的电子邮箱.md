# 题目概述

**题目描述**：  
表 `Person` 包含两列：

- `id` (int, 主键)
- `email` (varchar)

要求编写一个 SQL **删除语句 (DELETE)**，删除所有重复的电子邮箱，**只保留** `id` **最小** 的那一个。注意：不需要返回任何结果，直接修改原表。

**示例**：  
输入：

|   |   |
|---|---|
|id|email|
|1|john@example.com|
|2|bob@example.com|
|3|john@example.com|

输出（执行 DELETE 后）：

|   |   |
|---|---|
|id|email|
|1|john@example.com|
|2|bob@example.com|

解释：ID 1 和 ID 3 的邮箱相同，ID 1 小于 ID 3，所以保留 ID 1，删除 ID 3。

---

# 解题思路

这是一道经典的 **“自连接 (Self Join)”** 题目。我们需要比较同一张表里的不同行。

**逻辑分析**：  
我们需要找出那些 **“虽然邮箱相同，但 ID 比较大”** 的行，然后把它们删掉。

1. **比较条件**：

- 邮箱相同：`p1.email = p2.email`
- ID 更大：`p1.id > p2.id` (这里假设 p1 是要删除的那个，p2 是要保留的那个)

2. **筛选**：

- 如果我们把表 `Person` 自己和自己连接 (`Person p1 JOIN Person p2`)，满足上述条件的 `p1` 就是我们要删除的目标。
- 比如示例中：(p1.id=3, p1.email=john) 和 (p2.id=1, p2.email=john) 匹配上了，且 `3 > 1`，所以 `p1` (id=3) 要被删掉。

---

# SQL 代码

```sql
DELETE p1
FROM Person p1
JOIN Person p2 ON p1.email = p2.email
WHERE p1.id > p2.id;
```

### 知识点扩展 (Pair Programming Tips)

- **DELETE 语法**:

- 在 MySQL 中，多表删除的语法是 `DELETE 别名 FROM 表1, 表2 ...`。
- `DELETE p1` 的意思是：只删除 `p1` 这张表（其实就是 Person 表）里匹配到的行，而不要动 `p2`。如果不写 `p1`，MySQL 会不知道你想删连接结果中的哪一边。

- **子查询解法 (不推荐但常见)**:

- 另一种思路是先查出“最小的 ID 列表”，然后删除不在这个列表里的 ID。
- `DELETE FROM Person WHERE id NOT IN (SELECT min_id FROM (SELECT MIN(id) AS min_id FROM Person GROUP BY email) AS t)`。
- 这种写法在 MySQL 中需要多包一层 `SELECT`（即 `AS t`），因为 MySQL 不允许一边查同一个表一边删它 (You can't specify target table 'Person' for update in FROM clause)。自连接写法通常性能更好且更简洁。