# 题目概述

题目涉及两张表：

1. `**Customer**` **表**：包含 `customer_id`（客户 ID）和 `product_key`（产品键）。注意，同一个客户可能多次购买同一个产品。
2. `**Product**` **表**：包含 `product_key`。这是商店里**所有**可用产品的列表。

**目标：** 编写一个 SQL 或程序逻辑，找出购买了 `Product` 表中**所有**产品的客户。

# 解题思路

要判断一个客户是否买了“所有”产品，最直观的逻辑就是：**比较数量**。

1. **统计总产品数 (****$N$****)：** 首先从 `Product` 表中计算出一共有多少个不同的产品。
2. **按客户分组：** 将 `Customer` 表按 `customer_id` 分组。
3. **统计客户购买的去重产品数 (****$M$****)：** 对于每个客户，计算他购买了多少种**不同**的产品（使用 `DISTINCT`）。
4. **过滤匹配：** 如果某个客户的 $M = N$，则说明该客户买下了所有产品。

# SQL代码

```sql
select
    customer_id 
from
    Customer c
group by c.customer_id
having count(distinct product_key) = (select count(*) from Product)  
```