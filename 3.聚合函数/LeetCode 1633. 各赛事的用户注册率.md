#子查询 

## 1. 题目概述

你有两张表：

`Users` **表**：包含所有用户的信息。

- `user_id`: 用户 ID。
- `user_name`: 用户姓名。

`Register` **表**：包含用户注册赛事的信息。

- `contest_id`: 赛事 ID。
- `user_id`: 注册该赛事的特定用户 ID。

**目标**：计算各赛事的**用户注册率**。  
**计算公式**：![](https://cdn.nlark.com/yuque/__latex/ef93d23bbba36f9939de6adbe41d3c1d.svg)  
**要求**：

1. 结果保留 **2 位小数**。
2. 按**百分比降序**排列；若百分比相同，按**赛事 ID 升序**排列。

---

## 2. 解题思路

这道题的难点在于分子（各赛事的注册数）是**随组变化的**，而分母（总用户数）是**全表固定**的。

1. **获取分母（总人数）**：  
    我们需要先知道 `Users` 表里一共有多少人。可以使用子查询：`(SELECT COUNT(*) FROM Users)`。
2. **获取分子（各赛事人数）**：  
    对 `Register` 表按 `contest_id` 进行分组（`GROUP BY`），然后使用 `COUNT(user_id)` 统计每个赛事的报名人数。
3. **计算百分比**：  
    将分子除以分母，乘以 100。注意使用 `ROUND(..., 2)` 处理精度。
4. **排序**：  
    先根据计算出的 `percentage` 字段 `DESC`（降序），再根据 `contest_id` 字段 `ASC`（升序）。

---

## 3. MySQL 代码实现

```
SELECT 
    contest_id, 
    ROUND(
        COUNT(user_id) * 100.0 / (SELECT COUNT(*) FROM Users), 
        2
    ) AS percentage
FROM 
    Register
GROUP BY 
    contest_id
ORDER BY 
    percentage DESC, 
    contest_id ASC;
```

count（user_id）是统计的Register按照contest_id进行分组之后，对应的user_id数量