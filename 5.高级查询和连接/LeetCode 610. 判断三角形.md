# 题目概述

**输入数据：** 一张名为 `Triangle` 的表，包含三列：`x`, `y`, `z`。这三列代表三条线段的长度。

**任务目标：** 我们需要生成一张结果表，包含原来的 `x`, `y`, `z` 列，并新增一列 `triangle`。

- 如果这三条线段能组成一个三角形，`triangle` 列显示 **'Yes'**。
- 否则，显示 **'No'**。

# 解题思路

### 数学原理

要判断三条线段能否组成三角形，必须满足 **三角形不等式定理**：

**任意两边之和必须大于第三边。**

也就是必须**同时**满足以下三个条件：

1. $x + y > z$
2. $x + z > y$
3. $y + z > x$

如果上述三个条件中哪怕有一个不满足（比如两条短边加起来等于或小于长边），这就无法构成封闭的三角形。

### SQL 实现逻辑

我们需要在查询过程中对每一行数据进行“判断”。在 SQL 中，处理这种 if-else 逻辑最通用的方法是使用 `**CASE WHEN**` 语句。

# SQL代码

```sql
# Write your MySQL query statement below
select
    x, y,z, (case when (x - y < z) and (x + y > z) and (x - z < y) and (x + z > y)
    and (y + z > x) and (y - z < x) then 'Yes' else 'No' end) as triangle 
from 
    Triangle t
```