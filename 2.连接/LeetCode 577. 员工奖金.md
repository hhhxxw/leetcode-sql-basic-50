这道题主要考察对 **外连接（Outer Join）** 和 **空值（NULL）处理** 的理解。

---

## 题目概述

需要从两张表 `Employee`（员工信息）和 `Bonus`（奖金信息）中，查询所有 **奖金少于 1000** 的员工姓名及其奖金金额。

**关键点：**

- **不仅** 要找出奖金在 0 到 1000 之间的员工。
- **还必须** 包含那些**没有任何奖金记录**（在 Bonus 表中不存在）的员工。

---

## 解题思路

这类问题的核心在于理解 SQL 中的“存在性”。

如果使用普通的 `INNER JOIN`，那么没有奖金记录的员工会被直接过滤掉。为了保留所有员工的信息，我们需要以 `Employee` 表为主表，使用 `LEFT JOIN`。

在 SQL 中，`NULL` 代表未知。如果直接写 `WHERE bonus < 1000`，数据库会跳过那些奖金为 `NULL` 的员工，因为 `NULL < 1000` 的判断结果既不是 True 也不是 False，而是 **Unknown**。

因此需要处理两种符合条件的情况：

1. `bonus < 1000`（确实有奖金但比较少）
2. `bonus IS NULL`（根本没有奖金记录）

---

## SQL 代码

```sql
SELECT 
    e.name, 
    b.bonus
FROM 
    Employee e
LEFT JOIN 
    Bonus b ON e.empId = b.empId
WHERE 
    b.bonus < 1000 OR b.bonus IS NULL;
```