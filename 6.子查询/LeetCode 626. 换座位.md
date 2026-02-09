# 题目概述

**题目场景**：  
有一张 `Seat` 表，里面记录了学生的座位号 `id` 和姓名 `student`。我们需要**交换相邻两个学生的座位**。

- 如果学生总数是奇数，最后一个学生的座位**不用换**。
- 返回的结果表必须按 `id` **升序**排序。

**输入示例**：

|   |   |
|---|---|
|id|student|
|1|Abbot|
|2|Doris|
|3|Emerson|
|4|Green|
|5|Jeames|

**预期输出**：

|   |   |
|---|---|
|id|student|
|1|Doris (2号换过来了)|
|2|Abbot (1号换过来了)|
|3|Green (4号换过来了)|
|4|Emerson (3号换过来了)|
|5|Jeames (最后一个人不动)|

---

# 解题思路

这道题的核心不在于真的去“交换数据”，而在于**重新计算 id**。我们可以通过 `CASE WHEN` 语句根据当前的 `id` 是奇数还是偶数，来推算出它“应该”变成的新 `id`。

#### 逻辑推导：

1. **对于奇数 ID (1, 3, 5...)**：

- 如果它是**最后一个** ID（比如示例中的 5），它应该保持不变 (`id = id`)。
- 如果它不是最后一个 ID（比如 1, 3），它应该变成下一个偶数 (`id = id + 1`)。

2. **对于偶数 ID (2, 4...)**：

- 它总是应该变成前一个奇数 (`id = id - 1`)。

#### CASE WHEN (最推荐)

- 计算总行数 `counts`。
- 如果 `id % 2 = 1` 且 `id = counts` -> 不变。
- 如果 `id % 2 = 1` 且 `id != counts` -> `id + 1`。
- 如果 `id % 2 = 0` -> `id - 1`。
- 最后按新 id 排序（或者在 SELECT 里直接交换 student 的名字，保持 id 不变）。

---

# SQL 代码

#### CASE WHEN

```sql
SELECT
    CASE
        -- 如果是奇数，且是最后一行 -> 不变
        WHEN id % 2 = 1 AND id = (SELECT COUNT(*) FROM Seat) THEN id
        -- 如果是奇数，且不是最后一行 -> +1
        WHEN id % 2 = 1 THEN id + 1
        -- 如果是偶数 -> -1
        ELSE id - 1
    END AS id,
    student
FROM Seat
ORDER BY id;
```