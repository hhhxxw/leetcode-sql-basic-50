重点：如何处理仅在某一时间段出售

# 题目概述

### 1. 数据结构

你需要处理两张表：

- **Product (产品表)**:

- `product_id` (主键)
- `product_name`
- `unit_price`

- **Sales (销售表)**:

- `seller_id`
- `product_id` (外键)
- `buyer_id`
- `sale_date` (销售日期)
- `quantity`, `price`

### 2. 目标

报告所有 **仅** 在 **2019-01-01** 到 **2019-03-31** 之间（也就是 2019 年第一季度）出售的产品 ID 和产品名称。

# 解题思路

这里有两种主流的思考模型，推荐使用第一种（聚合极值法），因为它写起来最简洁，执行效率通常也更高。

### 聚合极值法

如果一个产品 **只** 在 Q1（第一季度）出售，那么意味着：

1. 该产品的 **最早** 销售日期 (`MIN`) 必须 大于等于 `2019-01-01`。
2. 该产品的 **最晚** 销售日期 (`MAX`) 必须 小于等于 `2019-03-31`。

只要同时满足这两个条件，就排除了那些“跨季度销售”或者“完全在区间外销售”的情况。

**步骤：**

1. 将 `Sales` 表和 `Product` 表连接（JOIN），以便获取产品名称。
2. 按 `product_id` 分组（GROUP BY）。
3. 使用 `HAVING` 子句检查 `MIN(sale_date)` 和 `MAX(sale_date)` 是否都在区间内。

# MySQL代码

```sql
SELECT 
    p.product_id, 
    p.product_name
FROM 
    Product p
JOIN 
    Sales s
ON 
    p.product_id = s.product_id
GROUP BY 
    p.product_id, p.product_name
HAVING 
    MIN(s.sale_date) >= '2019-01-01' 
    AND 
    MAX(s.sale_date) <= '2019-03-31';
```

这里必须使用having， 如果使用where就会丢掉非范围中的数据，但是题目中要求的是仅，也就是最大值和最小值都在这个区间，所以应该先分组后用having