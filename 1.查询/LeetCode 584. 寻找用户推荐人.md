## 题目描述

表：`Customer`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
```

- `id` 是该表的主键列
- 该表的每一行表示一个客户的 id、姓名以及推荐他们的客户的 id

**任务**：  
找出那些**没有被 id = 2 的客户推荐**的客户的姓名。

以**任意顺序**返回结果表。

## 解题思路

需要**同时**考虑两种情况：

1. `referee_id` 不等于 2
2. `referee_id` 是 NULL

---

## SQL 代码

```sql
SELECT name
FROM Customer
WHERE referee_id != 2 
   OR referee_id IS NULL;
```

**说明**：

- `referee_id != 2`：处理不是2的情况
- `OR referee_id IS NULL`：处理NULL的情况
- 两个条件满足任意一个即可
