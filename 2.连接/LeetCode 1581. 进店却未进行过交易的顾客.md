## 题目概述

有两张表：

- **Visits(visit_id, customer_id)**：记录每一次进店访问（一次访问一个 visit_id，对应一个 customer_id）
- **Transactions(transaction_id, visit_id, amount)**：记录交易。每条交易通过 **visit_id** 关联到一次访问（表示该次进店产生了交易）

目标：**找出每个顾客“进店但没有发生交易”的访问次数**，输出：

- customer_id
- count_no_trans：该顾客没有交易的访问次数

关键点：**同一次 visit 可能有 0 笔交易或多笔交易**；我们要统计的是**没有任何交易记录的 visit**。

---

## 解题思路

要找“有访问但无交易”的 visit，经典写法如下：

- 先把 Visits 左连接 Transactions（按 visit_id）
- 如果某次 visit 没有交易，那么右表字段会是 **NULL**
- 过滤出 `t.visit_id IS NULL` 的行 → 这些就是“未交易的访问”
- 再按 customer_id 分组计数

---

## SQL 代码

```sql
SELECT
    v.customer_id,
    COUNT(*) AS count_no_trans
FROM Visits v
LEFT JOIN Transactions t
    ON v.visit_id = t.visit_id
WHERE t.visit_id IS NULL
GROUP BY v.customer_id;
```