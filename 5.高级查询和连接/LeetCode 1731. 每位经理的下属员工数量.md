# 题目概述

**题目背景：** 我们有一张 `Employees` 表，包含员工 ID、姓名、汇报对象（`reports_to`）以及年龄。

**核心任务：** 我们需要找出所有的 **“经理”**。在该题目的定义中，经理是 **至少有一个其他员工向其汇报** 的员工。

**输出要求：** 对于每位经理，输出其：

1. `employee_id`（经理 ID）
2. `name`（经理姓名）
3. `reports_count`（向其汇报的员工总数）
4. `average_age`（向其汇报的员工的平均年龄，**四舍五入到最近的整数**）

最后结果按 `employee_id` 升序排列。

# 解题思路

这道题的关键在于理解 **同一张表里有两种角色**：下属（提交报告的人）和经理（接收报告的人）。

### 核心步骤：

1. **确定连接条件：** 我们需要将表自连接。设 `e1` 为下属表，`e2` 为经理表。连接条件是：`e1.reports_to = e2.employee_id`。

- 这样，每一行数据都会把“下属”和他的“经理”拼在一起。

2. **分组统计：** 按经理的信息（`e2.employee_id` 和 `e2.name`）进行分组 (`GROUP BY`)。
3. **聚合计算：**

- `COUNT(e1.employee_id)`：统计有多少个下属。
- `AVG(e1.age)`：计算下属的平均年龄。
- 使用 `ROUND(..., 0)` 处理四舍五入。

4. **排序：** 使用 `ORDER BY employee_id` 满足题目格式要求。

# SQL代码

```sql
select 
    e2.employee_id, e2.name, count(*) as reports_count, round(AVG(e1.age), 0) as average_age 
from 
    Employees e1
join
    Employees e2
on e1.reports_to = e2.employee_id
group by e2.employee_id, e2.name  # 非聚合的列都建议放入
order by e2.employee_id
```