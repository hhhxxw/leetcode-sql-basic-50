## 题目概述

有两张表：

- `Project` **表**：记录项目与员工的对应关系。

	- `project_id`: 项目 ID。
	- `employee_id`: 员工 ID。

- `Employee` **表**：记录员工的详细信息。

	- `employee_id`: 员工 ID。
	- `name`: 员工姓名。
	- `experience_years`: **工作年限**（这是计算核心）。

**目标**：查询每一个项目（`project_id`）中所有员工的**平均工作年限**。  
**要求**：结果保留 **2 位小数**。

---

## 解题思路

可以将解题步骤分解为以下三步：

1. **关联数据（Join）**：  
    由于项目信息在 `Project` 表，而工作年限在 `Employee` 表，我们需要通过 `employee_id` 将两张表连接起来。这里使用 `INNER JOIN` 即可，因为只有同时存在于两表的记录才有意义。
2. **分组汇总（Group By）**：  
    我们需要按项目计算，因此需要使用 `GROUP BY project_id`，将同一个项目的所有员工归为一组。
3. **计算平均值（AVG & ROUND）**：

- 使用 `AVG(experience_years)` 计算该组内年限的平均值。
- 使用 `ROUND(..., 2)` 将计算出的平均值四舍五入保留两位小数。

---

## 3. SQL 代码实现

```sql
SELECT 
    p.project_id, 
    ROUND(AVG(e.experience_years), 2) AS average_years
FROM 
    Project p
JOIN 
    Employee e 
ON 
    p.employee_id = e.employee_id
GROUP BY 
    p.project_id;
```