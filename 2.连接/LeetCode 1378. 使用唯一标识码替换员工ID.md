### 题目概述

有两张表：

**Employees**

- `id`：员工 ID
- `name`：员工姓名

**EmployeeUNI**

- `id`：员工 ID
- `unique_id`：员工唯一标识码（可能不是每个员工都有）

要求：输出每个员工的 `unique_id` 和 `name`。  
如果某个员工在 `EmployeeUNI` 里没有对应记录，则 `unique_id` 输出 `NULL`。

### 解题思路

- 以 `Employees` 为主表，确保所有员工都要输出。
- 用 `LEFT JOIN` 连接 `EmployeeUNI`，连接条件是 `Employees.id = EmployeeUNI.id`， 这里采用左连接保证了name一定全包含，name对应没有unique_id则用null表示，符合题意
- 选择输出列：`unique_id, name`

### SQL代码

```sql
select e2.unique_id, e1.name from Employees e1 left join EmployeeUNI e2 on e1.id = e2.id;
```