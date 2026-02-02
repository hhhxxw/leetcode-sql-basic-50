# 题目概述

有一张 `Queries` 表，记录了用户搜索的情况：

- `query_name`: 查询的名称（如 "Dog", "Cat"）。
- `result`: 搜索结果的描述。
- `position`: 该结果在搜索列表中的位置（1 到 500）。
- `rating`: 用户对该搜索结果的评分（1 到 5）。

**你需要计算两个指标：**

1. **质量 (Quality)**：各查询名称评分与位置比值的平均值。

- 公式：![](https://cdn.nlark.com/yuque/__latex/d1746a5f4e20bc9ebc3dfaf4afce311e.svg)

2. **劣质查询占比 (Poor Query Percentage)**：评分 **小于 3** 的查询所占的百分比。

- 公式：![](https://cdn.nlark.com/yuque/__latex/70b9bce3fb13c05bbcee4a643425ab33.svg)

**要求**：结果均保留 **2 位小数**。

# 解题思路

解题的关键在于如何在一个 `SELECT` 语句中，既处理“数值计算”又处理“条件计数”。

### 处理“质量”

这部分比较直接：

- 直接对每一行计算 `rating / position`。
- 使用 `AVG()` 函数求平均值。
- 使用 `ROUND(..., 2)` 保留小数。

### 处理“占比”（核心技巧）

如何统计“评分小于 3 的行数”？这里有两种高效的 SQL 技巧：

- 使用 `AVG(rating < 3)` **(MySQL 特有)** 在 MySQL 中，布尔表达式 `rating < 3` 会返回 1（真）或 0（假）。对一堆 1 和 0 求平均值，结果正好就是满足条件的项目占比。

### 分组

- 使用 `GROUP BY query_name`，确保每个搜索词（如 "Dog"）都有自己的一行统计结果。
- **注意**：如果 `query_name` 为空，通常需要过滤掉（根据题目具体要求）。

# MySQL代码

```sql
SELECT 
    query_name,
    -- 计算质量：评分/位置 的平均值
    ROUND(AVG(rating / position), 2) AS quality,
    -- 计算占比：利用 MySQL 布尔值求平均的特性
    ROUND(AVG(rating < 3) * 100, 2) AS poor_query_percentage
FROM 
    Queries
WHERE 
    query_name IS NOT NULL
GROUP BY 
    query_name;
```

这里poor_query_percentage用的是占比，所以用rating < 3这种布尔值是可以的

`AVG()` 函数的本质是：![](https://cdn.nlark.com/yuque/__latex/673d64b32e981df26e0dfa869e79fa1a.svg)。

当我们将 `AVG()` 作用于这组布尔值（0, 1, 1）时，计算过程如下：![](https://cdn.nlark.com/yuque/__latex/b5f6e4c6e0756ec412d3e074cace0a0d.svg)