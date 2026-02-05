## 题目概述

表：`Activity`

常见字段：

- `player_id`
- `device_id`
- `event_date`（玩家登录日期）
- `games_played`

要求：计算 **次日留存率（fraction）**  
也就是：对每个玩家，找出他的**首次登录日期** `first_date`，看他是否在 **first_date 的下一天** 也登录过。  
最终输出：

- `fraction` = 满足“次日登录”的玩家数 / 玩家总数  
    通常保留 **2 位小数**。

---

## 解题思路

1. **先找每个玩家的首次登录日期**

- `SELECT player_id, MIN(event_date) AS first_date FROM Activity GROUP BY player_id`

2. **判断是否存在“次日登录”记录**

- 把上一步的结果回连到 `Activity`
- 条件：`a.event_date = DATE_ADD(first_date, INTERVAL 1 DAY)`
- 只要能连上，说明该玩家次日有登录

3. **算比例**

- 分子：次日有登录的玩家数（distinct player）
- 分母：玩家总数
- 用 `COUNT(DISTINCT ...) / COUNT(DISTINCT ...)` 或者用 `AVG(0/1)` 都行
- `ROUND(..., 2)` 保留两位小数

---

## MySQL 代码

```sql
SELECT
  ROUND(
    COUNT(DISTINCT a.player_id) / (SELECT COUNT(DISTINCT player_id) FROM Activity),
    2
  ) AS fraction
FROM Activity 
JOIN (
  SELECT player_id, MIN(event_date) AS first_date
  FROM Activity
  GROUP BY player_id
) f
  ON a.player_id = f.player_id
 AND a.event_date = DATE_ADD(f.first_date, INTERVAL 1 DAY);
```