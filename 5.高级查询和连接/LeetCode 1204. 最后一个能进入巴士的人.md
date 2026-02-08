>我一开始按照turn进行order排序得到一张表，这里关键是要计算累积重量

# 题目概述

有一张 `Queue` 表，包含以下字段：

- `person_name`: 乘客姓名。
- `weight`: 乘客重量。
- `turn`: 上车顺序（按此从小到大上车）。

**目标：** 巴士的最大载重限制为 **1000kg**。你需要找到按照 `turn` 顺序上车时，最后一个重量累计未超过 1000kg 的乘客姓名。

# 解题思路

解决这个问题的核心逻辑分为三步：

1. **计算累计重量（Running Total）：** 按照 `turn` 的升序，计算每一行及其之前所有人的重量总和。
2. **筛选符合条件的记录：** 过滤掉累计重量超过 1000kg 的行，只保留累计重量 1000 的乘客。
3. **提取目标值：** 在符合条件的乘客中，找到 `turn` 最大（也就是最后上车）的那个人。

### 核心技巧：窗口函数 `SUM() OVER()`

什么窗口函数？

聚合函数的窗口函数就是聚合函数后面加上over

- `SUM()`: 计算累计总和（如你之前问的 LeetCode 1204 题）。
- `AVG()`: 计算移动平均值。
- `MAX() / MIN()`: 找出当前窗口内的最大/最小值。

公式![](https://cdn.nlark.com/yuque/__latex/5fd458f9b25d9f7835ccfe3810939a66.svg)

我们可以使用 SQL 的窗口函数来轻松计算累计和：

`SUM(weight) OVER (ORDER BY turn ASC)`

这会为每一行计算从第一行到当前行的 `weight` 总和。

# SQL代码

```
SELECT person_name
FROM (
    SELECT person_name, 
           SUM(weight) OVER (ORDER BY turn ASC) as total_weight
    FROM Queue
) AS t
WHERE total_weight <= 1000
ORDER BY total_weight DESC
LIMIT 1;
```