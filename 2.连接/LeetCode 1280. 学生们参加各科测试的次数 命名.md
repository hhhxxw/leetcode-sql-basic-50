## 题目概述

给定三张表：

**Students 表**

```
student_id | student_name
```

记录所有学生信息，每个学生有唯一 `student_id`。

**Subjects 表**

```
subject_name
```

记录所有科目名称，每个科目唯一。

**Examinations 表**

```
student_id | subject_name
```

记录学生参加某一科目的考试，每条记录代表该学生参加了一次该科目的考试，这个表中可能有重复记录。

要求输出 **每个学生参加每一门科目测试的次数**，即：

- 结果包含所有学生和所有科目组合（包括未参加考试的情况，次数为 0）。
- 输出按 `student_id` 和 `subject_name` 排序。

输出表结构如下：

|   |   |   |   |
|---|---|---|---|
|student_id|student_name|subject_name|attended_exams|

---

## 解题思路

解决本题的关键在于：

### 得到 **所有学生 × 所有科目** 的组合

因为即使某学生未参加某科考试，也要输出次数为 0 的记录。  
可以用 **笛卡尔积（CROSS JOIN）** 将 Students 和 Subjects 表组合得到所有可能的 (学生, 科目) 对。

`CROSS JOIN Subjects`：生成每个学生所有科目的组合。

### 将组合结果与 Examinations 表连接

用 **LEFT JOIN** 将笛卡尔积的每个 (学生, 科目) 对与考试记录匹配，能保证没有考试记录的组合仍然保留。

### 使用 **COUNT** 统计次数

对于匹配的考试记录，按 `(student_id, subject_name)` 统计出现次数作为 `attended_exams`。  
注意使用 `COUNT(e.subject_name)` 或 `COUNT(e.student_id)` 而不是 `COUNT(*)`，以确保未参加记录计为 0。

count（字段名称）这是统计字段的非空值

### 最后 **按 student_id 和 subject_name 排序** 输出结果。

---

## MySQL 代码

```
SELECT 
    s.student_id,
    s.student_name,
    sub.subject_name,
    COUNT(e.subject_name) AS attended_exams
FROM 
    Students s
CROSS JOIN 
    Subjects sub
LEFT JOIN 
    Examinations e
    ON s.student_id  =  e.student_id
    AND sub.subject_name  =  e.subject_name
GROUP BY 
    s.student_id,
    s.student_name,
    sub.subject_name
ORDER BY 
    s.student_id,
    sub.subject_name;
```