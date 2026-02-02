## 题目概述

有两张表：

- `Prices` **表**：记录了产品在不同时间段的单价。

- `product_id`: 产品 ID。
- `start_date`, `end_date`: 该价格生效的起止日期。
- `price`: 该时间段内的价格。

- `UnitsSold` **表**：记录了产品的实际销售情况。

- `product_id`: 产品 ID。
- `purchase_date`: 购买日期。
- `units`: 购买数量。

**目标**：计算每个产品的平均售价。  
**公式**：![](https://cdn.nlark.com/yuque/__latex/037b13355109b549fac6a2224cdfaa89.svg) 。**注意**：结果需保留两位小数。如果没有销售记录，平均售价应为 0。

---

## 解题思路

计算平均售价的核心在于如何将“销售日期”匹配到正确的“价格区间”内。

1. **左连接 (LEFT JOIN)**：  
    我们需要以 `Prices` 表为主表进行左连接，确保即使某个产品没有销售记录，它依然能出现在结果中（这是处理“无销售记录显示为0”的关键）。
2. **连接条件**：  
    连接不仅要匹配 `product_id`，还要判断销售日期是否在价格的有效期内：  
    `UnitsSold.purchase_date BETWEEN Prices.start_date AND Prices.end_date`
3. **计算总和与分组**：  
    使用 `GROUP BY product_id` 对每个产品进行分组。

- 分子：`SUM(price * units)`
- 分母：`SUM(units)`

4. **处理空值 (NULL)**：  
    如果一个产品没卖出去，`SUM(units)` 会是 NULL。我们需要使用 `IFNULL(..., 0)` 函数将计算结果转换为 0。
5. **格式化**：  
    使用 `ROUND(..., 2)` 保留两位小数。

---

## SQL 代码

```
SELECT 
    p.product_id, 
    IFNULL(ROUND(SUM(p.price * u.units) / SUM(u.units), 2), 0) AS average_price
FROM 
    Prices p
LEFT JOIN 
    UnitsSold u 
ON 
    p.product_id = u.product_id 
    AND u.purchase_date BETWEEN p.start_date AND p.end_date
GROUP BY 
    p.product_id;
```