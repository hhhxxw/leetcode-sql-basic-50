# 题目概述

### 1. 数据结构

你需要处理一张名为 `Teacher` 的表，其包含以下字段：

- `teacher_id` (Int): 教师的 ID。
- `subject_id` (Int): 该教师教授的科目 ID。
- `dept_id` (Int): 该科目所属的部门 ID。

### 2. 目标

计算每位教师所教授的 **不同** 科目种类的数量。

### 3. 输出要求

- 返回结果表，需包含 `teacher_id` 和 `cnt`（不同科目的数量）。
- 结果可以按任意顺序排列。

# 解题思路

- **分组 (Grouping):** 题目要求按 "每位教师" 统计，这意味着我们需要以 `teacher_id` 作为维度将数据分组。在 SQL 中，这对应 `GROUP BY` 子句。
- **去重 (Deduplication):** 题目要求统计 "科目种类"（即不同的科目）。如果一位老师在不同部门教同一个科目（虽然在现实中较少见，但逻辑上需防范重复），或者表中存在冗余行，直接计数会导致错误。因此，必须使用去重统计。在 SQL 中，这对应 `DISTINCT` 关键字。
- **聚合 (Aggregation):** 最后需要计算数量，对应 `COUNT` 函数。

# SQL代码

```sql
select 
    teacher_id, count(distinct subject_id) as cnt
from
    Teacher t
group by teacher_id;
```