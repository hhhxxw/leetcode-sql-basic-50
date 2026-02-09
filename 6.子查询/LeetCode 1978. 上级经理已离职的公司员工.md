# 题目概述

需要找出那些 **“工资很低（< 30000）”** 且 **“上级经理已经离职（不存在于员工表中）”** 的员工 ID。

**输入表** `Employees`：

|   |   |   |
|---|---|---|
|列名|类型|含义|
|employee_id|int|员工 ID (主键)|
|name|varchar|员工姓名|
|manager_id|int|上级经理的 ID (可能为 NULL)|
|salary|int|工资|

**这里找的是上级经理已经离职的员工，而CEO没有上级经理所以不属于这一类人**

**输出要求**：

- 返回 `employee_id`。
- 按 `employee_id` **从小到大排序**。

---

# 解题思路

这个问题可以拆解为三个筛选条件：

1. **工资条件**：`salary < 30000`。
2. **有经理**：`manager_id IS NOT NULL`（如果经理是 NULL，说明他是 CEO，不需要找经理是否离职）。
3. **经理已离职**：这是核心难点。意味着该员工的 `manager_id` 在 `Employees` 表的 `employee_id` 列中 **找不到**。

#### `LEFT JOIN`

- 思路：让员工表（左表）去关联员工表（右表），关联条件是 `左表.manager_id = 右表.employee_id`。
- 筛选：如果关联后右表的 `employee_id` 是 `NULL`，说明左表这个员工的经理在右表里没找到（即经理离职了）。

---

### SQL 代码实现

使用 `LEFT JOIN` (推荐生产环境/面试)

在处理大数据量时，`LEFT JOIN` 通常比 `NOT IN` 性能更优（尤其是在 MySQL 中，`NOT IN` 遇到 NULL 值可能有坑，虽然题目保证了 ID 不为 NULL，但 JOIN 是更通用的解法）。

```sql
SELECT e1.employee_id
FROM Employees e1
LEFT JOIN Employees e2 ON e1.manager_id = e2.employee_id
WHERE e1.salary < 30000        -- 员工本人薪资低
  AND e1.manager_id IS NOT NULL -- 员工本人有经理
  AND e2.employee_id IS NULL    -- 关键：关联不到经理（经理不在员工表里）
ORDER BY e1.employee_id;
```