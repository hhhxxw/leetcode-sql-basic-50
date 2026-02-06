# 题目概述

1. 数据结构

你需要处理一张名为 Activity 的表，包含以下字段：

- user_id (Int): 用户 ID。
- session_id (Int): 会话 ID。
- activity_date (Date): 活动日期。
- activity_type (Enum): 活动类型（如 open_session, end_session, scroll_down, send_message）。

2. 目标

统计 截至 2019-07-27（含） 的过去 30 天内，每天的活跃用户数量。

注意：

- 活跃用户 指在某一天至少进行了一次活动的用户。
- 结果只需包含有记录的日期（不需要补全为 0 的日期）。

# 解题思路

这道题由两个核心步骤组成：**时间筛选** 和 **分组统计**。

### 1. 确定时间范围

题目要求统计 "截至 2019-07-27 的近 30 天"。

- **结束日期**: 2019-07-27
- **开始日期**: 2019-06-28 (2019-07-27 往前推 29 天，共计 30 天)
- **筛选条件**: `activity_date` 必须在这个闭区间内。

### 2. 去重统计

同一个用户在同一天可能有多条记录（比如多次打开会话、滚动页面）。题目问的是 "活跃用户数" (DAU - Daily Active Users)，而不是 "活跃次数"。

- 因此必须使用 `DISTINCT user_id` 来避免重复计算同一个用户。

### 3. 分组

题目要求 "每天" 的数据，所以需要按照 `activity_date` 进行 `GROUP BY`。

# SQL代码

```
SELECT
    activity_date AS day,
    COUNT(DISTINCT user_id) AS active_users
FROM
    Activity
WHERE
    activity_date BETWEEN '2019-06-28' AND '2019-07-27'
GROUP BY
    activity_date;
```

# 总结

这里我一开始做的是

```sql
select 
    activity_date as day, count(distinct user_id) as active_users 
from
    Activity
group by activity_date
having day <= '2019-07-27' and day>= '2019-6-28'
```

这里涉及到having和where的使用区别

![](https://cdn.nlark.com/yuque/0/2026/png/40921502/1770346093984-c2f336d7-18e6-4b9e-86be-3dcbb95695ce.png)

通过上面这个SQL执行顺序，可以发现where是在分组之前执行，having是在分组之后执行

- `HAVING` 是在分组（GROUP BY）**之后** 运行的。这意味着数据库必须先扫描全表，把所有日期的具体数据都统计出来，最后再把不在范围内的数据丢掉。
- `WHERE` 是在分组 **之前** 运行的。它会先排除掉无关的日期，只处理你需要的数据，效率高得多。

这样就明白了