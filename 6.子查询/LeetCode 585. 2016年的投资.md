# 题目概述

题目链接：[585. 2016年的投资 - 力扣（LeetCode）](https://leetcode.cn/problems/investments-in-2016/?envType=study-plan-v2&envId=sql-free-50)

**题目场景**：  
有一张 `Insurance` 表，记录了保险公司的保单信息。

- `pid`: 保单 ID。
- `tiv_2015`: 2015 年的总投保金额。
- `tiv_2016`: 2016 年的总投保金额。
- `lat`, `lon`: 投保人的经纬度位置。

**筛选条件**：  
我们要找出符合以下**两个条件**的保单，并计算它们 **2016 年总投保金额 (**`tiv_2016`**) 的总和**：

1. **投保金额重复**：该保单在 2015 年的投保金额 (`tiv_2015`) 必须与表中**至少一个其他保单**相同。
2. **位置唯一**：该保单的经纬度组合 `(lat, lon)` 必须是表中**独一无二**的（即不能有其他保单在同一个位置）。

**输出**：

- 结果保留两位小数。

---

# 解题思路

这道题其实是考查 **“分组计数”** 和 **“窗口函数”** 的使用。我们需要对每一行数据进行两个维度的检查。

#### 方法一：使用窗口函数 `COUNT() OVER()` (推荐)

这是最简洁高效的方法，只需扫描一次表（或几次简单的窗口操作）。

1. **检查金额重复**：计算每个 `tiv_2015` 在全表中出现的次数。

- `COUNT(*) OVER (PARTITION BY tiv_2015)`
- 如果这个计数 `> 1`，说明有其他人跟它金额一样。

2. **检查位置唯一**：计算每个 `(lat, lon)` 组合在全表中出现的次数。

- `COUNT(*) OVER (PARTITION BY lat, lon)`
- 如果这个计数 `= 1`，说明它是唯一的。

3. **筛选求和**：

- 在外层查询中，筛选出 `cnt_amount > 1` 且 `cnt_location = 1` 的行。
- 对这些行的 `tiv_2016` 求和。

#### 方法二：使用 `IN` 和 `GROUP BY` (传统解法)

这个解法比较好理解

如果数据库不支持窗口函数，可以用子查询。

1. **找出重复的金额**：

- `SELECT tiv_2015 FROM Insurance GROUP BY tiv_2015 HAVING COUNT(*) > 1`

2. **找出唯一的位置**：

- `SELECT lat, lon FROM Insurance GROUP BY lat, lon HAVING COUNT(*) = 1`

3. **主查询**：

- `WHERE tiv_2015 IN (...) AND (lat, lon) IN (...)`

---

# SQL 代码

#### 方案 1：窗口函数 (MySQL 8.0+, PostgreSQL)

代码清晰，性能好。

```sql
SELECT ROUND(SUM(tiv_2016), 2) AS tiv_2016
FROM (
    SELECT 
        tiv_2016,
        -- 统计该金额出现的总次数
        COUNT(*) OVER (PARTITION BY tiv_2015) AS cnt_amount,
        -- 统计该位置出现的总次数
        COUNT(*) OVER (PARTITION BY lat, lon) AS cnt_loc
    FROM Insurance
) t
WHERE cnt_amount > 1  -- 条件1：金额至少跟别人重复一次
  AND cnt_loc = 1;    -- 条件2：位置必须是唯一的
```

#### 方案 2：GROUP BY 子查询 (通用解法)

适用于老版本数据库。

```sql
SELECT ROUND(SUM(tiv_2016), 2) AS tiv_2016
FROM Insurance
WHERE tiv_2015 IN (
    -- 找出所有重复的 2015 金额
    SELECT tiv_2015
    FROM Insurance
    GROUP BY tiv_2015
    HAVING COUNT(*) > 1
)
AND (lat, lon) IN (
    -- 找出所有唯一的位置
    SELECT lat, lon
    FROM Insurance
    GROUP BY lat, lon
    HAVING COUNT(*) = 1
);
```

### 关键点总结

1. `COUNT(*) OVER` **的威力**：它可以在不改变原表行数的情况下，把“分组统计结果”附加在每一行后面，非常适合这种“既要看明细又要看统计”的场景。
2. **逻辑与 (**`AND`**)**：两个条件必须同时满足，缺一不可。
3. **精度处理**：最后求和时别忘了 `ROUND(..., 2)`。