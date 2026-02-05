# 题目概述

表：`Delivery`

常见字段：

- `delivery_id`
- `customer_id`
- `order_date`：下单日期
- `customer_pref_delivery_date`：用户期望送达日期

需要先梳理一下题目中，每一个名词的含义/定义

定义：

- **即时订单（immediate）**：下单日期=用户期望送达的日期，`order_date = customer_pref_delivery_date`
- **首单**：每个 `customer_id` 的最早 `order_date` 那一条（如果同一天多单，题目通常保证不会影响或按最早日期即可）

目标：

- 计算 **“首单是即时订单”的用户占比（百分比）**
- 结果字段通常为：`immediate_percentage`
- 四舍五入保留 **2 位小数**

# 解题思路

先用子查询找到所有用户的首单，再找到到首单满足 `order_date = customer_pref_delivery_date` 的数量去进行计算

这题核心就两步：

1. **找出每个用户的首单**

- `MIN(order_date)` 按 `customer_id` 分组得到首单日期
- 再回表把首单那行取出来（因为需要比较 `customer_pref_delivery_date`）

2. **在首单集合里算即时比例**

- 分子：首单满足 `order_date = customer_pref_delivery_date` 的数量
- 分母：首单总数量（也就是用户数）
- 百分比：`分子 / 分母 * 100`
- `ROUND(..., 2)` 保留两位小数

实现方式推荐：用子查询拿到 `customer_id` + `first_order_date`，再 join 回 `Delivery`

# SQL代码

```sql
SELECT
  ROUND(
    100 * AVG(CASE WHEN d.order_date = d.customer_pref_delivery_date THEN 1 ELSE 0 END),
    2
  ) AS immediate_percentage
FROM Delivery d
JOIN (
  SELECT customer_id, MIN(order_date) AS first_order_date
  FROM Delivery
  GROUP BY customer_id
) f
  ON d.customer_id = f.customer_id
 AND d.order_date = f.first_order_date;
```