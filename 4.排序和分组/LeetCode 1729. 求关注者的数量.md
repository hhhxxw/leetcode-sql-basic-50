# 题目概述

在给定的 `Followers` 表中，每一行包含 `user_id`（用户 ID）和 `follower_id`（关注者 ID）。我们需要计算**每个用户**拥有的**关注者数量**。

**表结构：**

- `user_id`: 用户的唯一标识。
- `follower_id`: 关注该用户的关注者 ID。

**输出要求：**

- 结果包含 `user_id` 和对应的关注者总数 `followers_count`。
- 结果需要按 `user_id` 升序排序。

# 解题思路

这道题的核心在于**分组（Grouping）和计数（Counting）**。

1. **分组 (GROUP BY)**：因为我们要计算的是“每个用户”的数量，所以需要根据 `user_id` 字段对数据进行分组。
2. **聚合 (COUNT)**：在每个分组内，统计 `follower_id` 的数量。
3. **排序 (ORDER BY)**：题目明确要求结果按 `user_id` 升序排列。

# SQL代码

```sql
SELECT 
    user_id, 
    COUNT(follower_id) AS followers_count
FROM 
    Followers
GROUP BY 
    user_id
ORDER BY 
    user_id ASC;
```