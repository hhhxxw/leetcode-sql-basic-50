## 题目概述

题目给两张表（LeetCode 常见定义）：

- `Sales(sale_id, product_id, year, quantity, price)`

- 记录某个产品在某一年（或某次销售记录）的销量与单价

- `Product(product_id, product_name)`

- 产品基础信息

要求输出：对每一条 `Sales` 记录，查询出对应的 `product_name`，并返回：

- `product_name`
- `year`
- `price`

也就是：**把销售表和产品表关联起来，用产品名替代 product_id，同时带上销售年份与价格。**

---

## 解题思路

关键点：

- `Sales.product_id` 对应 `Product.product_id`
- 每条销售记录都应该输出（Sales 是“事实表”，一般是主表）
- 因为销售记录一定有产品（题目通常保证），用 `INNER JOIN` 就够了（更稳妥也可以 `LEFT JOIN`，但题目语义通常是内连接）

步骤：

1. 从 `Sales` 表取出 `year`、`price`（以及 product_id 用来关联）
2. 连接 `Product` 表拿到 `product_name`
3. 按要求选择输出列

---

## SQL 代码

```sql
SELECT
    p.product_name,
    s.year,
    s.price
FROM Sales s
JOIN Product p
    ON s.product_id = p.product_id;
```

`JOIN` 在 MySQL 里默认就是 `INNER JOIN`。