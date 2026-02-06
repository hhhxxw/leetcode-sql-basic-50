# 题目概述

假设有一个 `Courses` 表，包含两列：`student`（学生名）和 `class`（课程名）。

**目标：** 编写一个 SQL 查询，找出所有 **至少有 5 名学生** 的课程。

# 解题思路

- **分组 (Grouping)：** 我们需要按课程 (`class`) 将数据聚合在一起，这样才能统计每门课有多少人。
- **计数 (Counting)：** 使用聚合函数 `COUNT(student)` 来计算每组（即每门课）中的学生总数。
- **筛选 (Filtering)：** 关键点在于，我们不能使用 `WHERE` 来筛选聚合后的结果。我们需要使用 `**HAVING**` 子句，专门用于过滤聚合后的数据，找出计数值 $\ge 5$ 的组。

# SQL代码

```mysql
# Write your MySQL query statement below
select 
    class 
from 
    Courses
group by class 
having count(student) >= 5
```