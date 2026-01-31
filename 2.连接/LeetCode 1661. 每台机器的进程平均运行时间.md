## 题目概述

有一张表 `Activity`，记录机器上进程的开始/结束事件，常见字段：

- `machine_id`
- `process_id`
- `activity_type`：`'start'` 或 `'end'`
- `timestamp`：事件发生时间（同一进程会有一条 start 和一条 end）

要求：对**每台机器**计算其进程的**平均运行时间**：

- 单个进程运行时间 = `end.timestamp - start.timestamp`
- 每台机器平均值 = 该机器所有进程运行时间的平均  
    输出：`machine_id` 和 `processing_time`（通常要求保留 3 位小数）

---

## 解题思路

**核心：把同一台机器、同一进程的 start 和 end 配对**，算出每个进程的耗时，再对机器求平均。

常见做法（MySQL）：

1. 自连接 `Activity` 表：一份当 start，一份当 end
2. 连接条件：

- `machine_id` 相同
- `process_id` 相同
- `activity_type` 分别为 `'start'` / `'end'`

3. 用 `end.timestamp - start.timestamp` 得到每个进程耗时
4. 按 `machine_id` 分组求 `AVG`
5. 用 `ROUND(..., 3)` 输出 3 位小数

---

## MySQL 代码

```sql
SELECT
    s.machine_id,
    ROUND(AVG(e.timestamp - s.timestamp), 3) AS processing_time
FROM Activity s
JOIN Activity e
  ON s.machine_id = e.machine_id
 AND s.process_id = e.process_id
 AND s.activity_type = 'start'
 AND e.activity_type = 'end'
GROUP BY s.machine_id;
```

如果你还想看另一种写法（用条件聚合 `MAX(CASE...) - MIN(CASE...)`），我也可以一并给你。