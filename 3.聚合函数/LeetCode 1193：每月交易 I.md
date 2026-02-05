## 题目概述

给你一张交易表 `Transactions`（常见字段：`id`, `country`, `state`, `amount`, `trans_date` 等）。

要求你**按“月份 + 国家”分组**统计每组的：

- `trans_count`：交易总笔数
- `approved_count`：状态为 `'approved'` 的交易笔数
- `trans_total_amount`：交易总金额
- `approved_total_amount`：状态为 `'approved'` 的交易总金额

输出字段里通常要把月份格式化成 `YYYY-MM`，列名与题目要求一致。

---

## 解题思路

核心就是 **GROUP BY month(trans_date), country**，然后用聚合函数做条件统计：

1. **月份字段 month**

- MySQL 用 `DATE_FORMAT(trans_date, '%Y-%m')` 得到 `YYYY-MM`,这里是将年月日，转换为年月，例如`2020-01-15` → `2020-01`

2. **总笔数**

- `COUNT(*)`，相同年月，相同国家的数量下统计trans_count

3. **条件笔数（approved_count）**

- 用 `SUM(CASE WHEN state='approved' THEN 1 ELSE 0 END)`

4. **总金额**

- `SUM(amount)`

5. **条件金额（approved_total_amount）**

- `SUM(CASE WHEN state='approved' THEN amount ELSE 0 END)`

注意点：

- `CASE WHEN ... THEN ... ELSE ... END` 这种写法是 MySQL 做“条件聚合”的标准套路。
- 输出列名必须和题目一致。

---

## MySQL 代码

```sql
SELECT
  DATE_FORMAT(trans_date, '%Y-%m') AS month,
  country,
  COUNT(*) AS trans_count,
  SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
  SUM(amount) AS trans_total_amount,
  SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY
  DATE_FORMAT(trans_date, '%Y-%m'),
  country;
```