主要考察的是 `SELECT` 查询和 `WHERE` 条件过滤的基本用法。

### 1. 题目概述

- **表名**：`Products`
- **字段**：
- `product_id` (int)：主键，产品的唯一标识。
- `low_fats` (enum)：枚举类型，取值为 'Y' (是) 或 'N' (否)，表示是否低脂。
- `recyclable` (enum)：枚举类型，取值为 'Y' (是) 或 'N' (否)，表示是否可回收。
- **目标**：查找既是**低脂**又是**可回收**的产品 ID。
- **返回结果**：结果表中的行顺序无要求。

---

### 2. 解题思路

1. **确定要查什么 (SELECT)**：题目要求返回产品 ID，所以是 `SELECT product_id`。
2. **确定从哪查 (FROM)**：数据源是 `Products` 表，所以是 `FROM Products`。
3. **确定筛选条件 (WHERE)**：

- 条件一：低脂 (`low_fats` 为 'Y')。
- 条件二：可回收 (`recyclable` 为 'Y')。
- 逻辑关系：两个条件必须**同时满足**，所以使用逻辑运算符 `AND`。
- **关键点**：比较具体的值（'Y'）时，必须使用 `=` 而不是 `IS`。

---

### 3. SQL 代码

```sql
SELECT product_id 
FROM Products 
WHERE low_fats = 'Y' 
  AND recyclable = 'Y';
```
