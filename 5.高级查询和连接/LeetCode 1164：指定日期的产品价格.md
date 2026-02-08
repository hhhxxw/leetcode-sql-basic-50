# 题目概述

**Products(product_id, new_price, change_date)**表示某个产品在 `change_date` 这一天开始，价格变为 `new_price`。

要求：给定日期（题目里通常是 `2019-08-16`），查询**每个 product** 在该日期当天的价格：

- 如果在该日期之前（含当天）有过多次改价，取**最近一次 change_date ≤ 指定日期**的那条记录的价格。
- 如果该产品在指定日期之前从未改价，则价格为 **10**（题目默认初始价格）。

输出：`product_id, price`

# 解题思路

核心就是：**对每个 product_id 找到 change_date ≤ 指定日期 的最大 change_date**，拿到对应 new_price。

难点在于：要把“没有任何符合日期的改价记录”的产品也输出（价格 10）。

常见解法有两类：

### 解法 A（推荐，兼容性强）

1. 先找出每个产品在指定日期前最后一次改价（用 `MAX(change_date)` 分组）。
2. 把这个结果回连到原表拿 `new_price`。
3. 再补上那些“在指定日期前没有改价记录”的产品，价格设为 10（用 `NOT IN` / `NOT EXISTS`）。

复杂度清晰，面试/刷题都常用。

### 解法 B（窗口函数）

用 `ROW_NUMBER()` 给每个 product 按 change_date 倒序排序，取第一条；再补默认 10。写起来更短，但依赖窗口函数支持。

# SQL代码

```
-- 1) 先找到每个 product 在指定日期之前(含当天)的最后一次改价日期
WITH last_change AS (
    SELECT
        product_id,
        MAX(change_date) AS last_date
    FROM Products
    WHERE change_date <= '2019-08-16'
    GROUP BY product_id
)

-- 2) 对于有改价记录的产品：取最后一次改价对应的 new_price
SELECT
    p.product_id,
    p.new_price AS price
FROM Products p
JOIN last_change lc
  ON p.product_id = lc.product_id
 AND p.change_date = lc.last_date

UNION

-- 3) 对于指定日期前从未改价的产品：默认价格 10
SELECT
    product_id,
    10 AS price
FROM Products
WHERE product_id NOT IN (SELECT product_id FROM last_change);
```