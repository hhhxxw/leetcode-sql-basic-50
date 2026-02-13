# 题目概述

**题目描述**：  
表 `Employee` 包含两列：

- `id` (int, 主键)
- `salary` (int)

要求编写一个 SQL 查询，获取 `Employee` 表中 **第二高** 的薪水（`SecondHighestSalary`）。  
**注意**：如果不存在第二高的薪水（例如表中只有一条记录），查询应该返回 `null`。

**示例**：  
输入：

|   |   |
|---|---|
|id|salary|
|1|100|
|2|200|
|3|300|

输出：

|   |
|---|
|SecondHighestSalary|
|200|

输入（只有一条）：

|   |   |
|---|---|
|id|salary|
|1|100|

输出：

|   |
|---|
|SecondHighestSalary|
|null|

---

# 解题思路

核心难点在于**如何处理“不存在”的情况并返回 NULL**。

**普通思路（有缺陷）**：

1. 去重：`DISTINCT salary`（防止两个 300 并列第一）。
2. 排序：`ORDER BY salary DESC`。
3. 分页：`LIMIT 1 OFFSET 1`（跳过第1个，取第2个）。

- _问题_：如果结果集只有一行（没有第二高），直接 `LIMIT 1 OFFSET 1` 会返回**空结果集**（Empty Result），而不是题目要求的 `null`。

**正确思路**：  
我们需要把查询结果包装一下，使其在为空时变成 `null`。

1. **方法一：使用子查询（推荐）**

- `SELECT (SELECT DISTINCT ... LIMIT 1 OFFSET 1) AS SecondHighestSalary`
- 在 MySQL 中，如果 `SELECT` 子句中的子查询没查到数据，它会自动返回 `NULL`，这完美符合题目要求

2. **方法二：使用** `IFNULL` **函数**

- `SELECT IFNULL((SELECT ...), null) AS SecondHighestSalary`
- 逻辑更显式，但稍微繁琐一点。

---

### SQL 代码

**解法 1：利用子查询的特性（最简洁）**

```
SELECT (
    SELECT DISTINCT salary 
    FROM Employee 
    ORDER BY salary DESC 
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;
```

**解法 2：使用** `IFNULL`**（更通用）**

```
SELECT IFNULL(
    (SELECT DISTINCT salary 
     FROM Employee 
     ORDER BY salary DESC 
     LIMIT 1 OFFSET 1), 
    NULL
) AS SecondHighestSalary;
```

### 知识点扩展 (Pair Programming Tips)

- **OFFSET vs LIMIT**:

- `LIMIT 1 OFFSET 1` 意思是：跳过前面 1 条，然后取 1 条。
- 等价写法：`LIMIT 1, 1` (注意逗号前是 offset，后是 limit，容易记反)。

- **第 N 高的薪水**:

- 这道题是特例，如果是通用的“第 N 高”，通常会写成存储过程，或者：
- `LIMIT 1 OFFSET N-1`。

- **窗口函数 (进阶)**:

- `DENSE_RANK() OVER (ORDER BY salary DESC)` 也可以解，适合处理更复杂的排名问题（比如并列排名），但对于简单的“第二高”来说稍微有点杀鸡用牛刀。