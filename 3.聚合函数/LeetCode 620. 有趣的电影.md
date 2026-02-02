# 题目概述

假设你有一张 `cinema` 表，包含以下列：

- `id`: 电影的主键 ID。
- `movie`: 电影名称。
- `description`: 电影描述。
- `rating`: 电影评分。

**目标**：编写一个 SQL 查询，找出满足以下**所有条件**的电影：

1. **ID 是奇数**。
2. **描述不是 "boring"**。
3. 结果需要按 **评分 (rating) 降序排列**。

# 解题思路

要解决这个问题，我们需要将三个逻辑要求转化为 SQL 语句：

### A. 如何判断奇数？

在 SQL 中，判断奇数通常有两种常用方法：

- **取模运算符 (%)：`id % 2 != 0` 或 `id % 2 = 1`。
- **MOD 函数**：`MOD(id, 2) = 1`。

### B. 如何过滤特定描述？

题目要求 `description` 不等于 "boring"。

- 使用**不等于运算符**：`description <> 'boring'` 或者 `description != 'boring'`。

### C. 如何排序？

题目要求按评分从高到低排列。

- 使用 `ORDER BY rating DESC`。

# MySQL代码

```sql
select * from cinema where description != "boring" and id % 2 = 1 
order by rating desc;
```