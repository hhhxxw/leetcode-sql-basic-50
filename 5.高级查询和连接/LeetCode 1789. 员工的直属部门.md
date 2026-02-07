>熟悉union：它的作用是合并结果并去重。虽然在这个特定题目逻辑下，第一部分和第二部分的数据理论上不会重叠（一个员工不可能既被标记为 Y，又只出现一次且标记为 N，除非数据异常），但使用 UNION 是安全的做法。

# 题目概述

**场景：** 并不是所有员工都只有一个部门。

- 有的员工属于多个部门，但在数据库中会通过 `primary_flag = 'Y'` 来标记哪一个是他的“直属部门”。
- 有的员工只属于一个部门，此时 `primary_flag` 默认为 'N'（或者题目没明确说，但逻辑上这个唯一的部门就是他的直属部门）。

**目标：** 我们要为 **每一位** 员工找到他的直属部门 ID。

**筛选规则：**

1. 如果 `primary_flag` 是 `'Y'`，选这行。
2. 如果该员工在表中只出现了一次（只有一个部门），选这行。

# 解题思路

这就好比我们在做分类整理，我们要找的人群分两拨：

- **人群 A（明确指定的）：** 那些有多部门，且明确标记了 'Y' 的行。
- **人群 B（唯一的）：** 那些虽然标记是 'N'，但因为他只属于这一个部门，所以必须选他的行。

针对这种逻辑，最直观的解法是 **“分而治之，最后合并”**。

### 核心步骤 (UNION 法)：

1. **查询 1：** 直接筛选 `primary_flag = 'Y'` 的记录。这解决了多部门员工的问题。
2. **查询 2：** 使用 `GROUP BY employee_id` 并配合 `HAVING COUNT(*) = 1`，找出那些只属于一个部门的员工。
3. **合并：** 使用 `UNION` 将上述两个结果集拼在一起。

# SQL代码

```
# Write your MySQL query statement below

-- 第一部分：找到明确标记为 'Y' 的直属部门
SELECT employee_id, department_id
FROM Employee
WHERE primary_flag = 'Y'

UNION

-- 第二部分：找到只属于一个部门的员工（这种情况下，该部门即为直属）
SELECT employee_id, department_id
FROM Employee
GROUP BY employee_id
HAVING COUNT(employee_id) = 1;
```