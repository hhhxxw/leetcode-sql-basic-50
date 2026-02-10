# 题目概述

**题目场景**：  
有一张 `Customer` 表，记录了顾客 ID、姓名、访问日期 `visited_on` 和消费金额 `amount`。  
需要计算：

- **7天移动平均值 (Moving Average)**：对于每一天，计算**当天及过去6天**（共7天）的总营业额和平均营业额。
- **输出范围**：只输出那些**能凑够7天**的日期（即从第7天开始输出）。
- **排序**：按日期升序。

**输出列**：  
`visited_on` (日期), `amount` (7天总和), `average_amount` (7天平均，保留2位小数)。

---

# 解题思路

这道题是典型的**滑动窗口 (Sliding Window)**问题。

#### 步骤 1：按天聚合 (Pre-aggregation)

原始表中，同一天可能有多个顾客消费。如果不先按天聚合，滑动窗口会很难算。

- 先把 `Customer` 表按 `visited_on` 分组，算出每一天的总金额 `daily_amount`。

#### 步骤 2：窗口计算 (Window Function)

需要对每一天，往前看 6 行（加上自己共 7 行），求 SUM 和 AVG。

- 使用 `SUM(amount) OVER (...)`
- 关键在于 `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW`

#### 步骤 3：过滤前 6 天 (Filtering)

因为题目要求“至少有7天数据”才能输出。

- 方法 A：算出最小日期 `min_date`，只输出 `visited_on >= min_date + 6` 的行。
- 方法 B：利用 `DENSE_RANK()` 或 `COUNT(*) OVER (...)` 辅助过滤（较麻烦）。
- 方法 C (推荐)：直接用 `LAG` 或者子查询算出的日期差。

---

### SQL 代码实现

#### 窗口函数 (推荐，适用于 MySQL 8.0+)

```sql
WITH DailyStats AS (
    -- 1. 先按天聚合，算出每天的总收入
    SELECT visited_on, SUM(amount) AS amount
    FROM Customer
    GROUP BY visited_on
),
WindowStats AS (
    -- 2. 使用窗口函数计算 7天移动求和
    SELECT 
        visited_on,
        SUM(amount) OVER (
            ORDER BY visited_on 
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS amount, -- 这里的 amount 变成了 7天总和
        ROUND(AVG(amount) OVER (
            ORDER BY visited_on 
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ), 2) AS average_amount
    FROM DailyStats
)
-- 3. 过滤掉前6天（数据不足7天的日期）
SELECT * 
FROM WindowStats
WHERE visited_on >= (
    SELECT DATE_ADD(MIN(visited_on), INTERVAL 6 DAY) 
    FROM Customer
)
ORDER BY visited_on;
```

如果使用自连接

```sql
SELECT 
    t1.visited_on,
    SUM(t2.amount) AS amount,
    ROUND(SUM(t2.amount) / 7, 2) AS average_amount
FROM (
    -- t1: 主表，只取第7天及以后的日期
    SELECT DISTINCT visited_on 
    FROM Customer
    WHERE visited_on >= (
        SELECT DATE_ADD(MIN(visited_on), INTERVAL 6 DAY) FROM Customer
    )
) t1
JOIN Customer t2 
    -- t2: 关联表，取 t1 日期及之前6天的数据
    ON DATEDIFF(t1.visited_on, t2.visited_on) BETWEEN 0 AND 6
GROUP BY t1.visited_on
ORDER BY t1.visited_on;
```