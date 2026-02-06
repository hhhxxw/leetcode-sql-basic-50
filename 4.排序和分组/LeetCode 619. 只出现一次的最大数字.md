# 题目概述

这道题要求从 `MyNumbers` 表中找出所有**只出现过一次**的数字，并在这些数字中找到**最大的**那一个。

- **表结构**：只有一个字段 `num` (Integer)。
- **特殊要求**：如果没有只出现一次的数字，则返回 `null`。

# 解题思路

- **筛选“孤独”的数字**： 我们需要找出那些在表中出现次数恰好为 1 的数字。这可以通过 `GROUP BY num` 结合 `HAVING COUNT(*) = 1` 来实现。
- **求最大值**： 在筛选出的数字集合中，使用 `MAX()` 函数获取最大值。

如何处理null

- 如果我们直接使用 `LIMIT 1` 配合 `ORDER BY`，当结果为空时，查询会返回“无结果（Empty Set）”，而不是 `null`。
- **解决方案**：将筛选逻辑作为**子查询**，并在外部套一个 `MAX()` 函数。由于聚合函数 `MAX()` 在处理空集时会天然地返回 `null`，这样能完美满足题目要求。

# SQL代码

```sql
SELECT 
    MAX(num) AS num
FROM (
    SELECT num
    FROM MyNumbers
    GROUP BY num
    HAVING COUNT(num) = 1
) AS unique_numbers;
```