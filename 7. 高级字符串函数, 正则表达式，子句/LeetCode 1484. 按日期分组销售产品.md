# 题目概述

**题目描述**：  
表 `Activities` 包含两列：

- `sell_date` (日期, date)
- `product` (产品名, varchar)
- 这张表没有主键，可能包含重复项（即同一天卖出同一种产品多次）。

要求编写一个 SQL 查询，查找每个日期的：

1. 销售的**不同产品数量** (`num_sold`)。
2. 销售的**产品名称列表** (`products`)，按字典序排序，并用逗号 `,` 分隔。

返回结果表按 `sell_date` 排序。

**示例**：  
输入：

|   |   |
|---|---|
|sell_date|product|
|2020-05-30|Headphone|
|2020-06-01|Pencil|
|2020-06-02|Mask|
|2020-05-30|Basketball|
|2020-06-01|Bible|
|2020-06-02|Mask|
|2020-05-30|T-Shirt|

输出：

|   |   |   |
|---|---|---|
|sell_date|num_sold|products|
|2020-05-30|3|Basketball,Headphone,T-Shirt|
|2020-06-01|2|Bible,Pencil|
|2020-06-02|1|Mask|

---

# 解题思路

这是一道考察 **分组聚合 (GROUP BY)** 和 **字符串拼接 (String Aggregation)** 的题目。

1. **分组**：既然要“按日期分组”，那就必须用 `GROUP BY sell_date`。
2. **去重计数**：计算不同产品的数量，不能直接 `COUNT(product)`，必须去重，所以用 `COUNT(DISTINCT product)`。
3. **字符串拼接**：

- 这是难点。我们需要把同一组内的多个产品名拼接成一个字符串。
- 在 MySQL 中，有一个神器函数叫 `GROUP_CONCAT()`。
- 它可以指定去重 (`DISTINCT`)、排序 (`ORDER BY`) 和分隔符 (`SEPARATOR`)。
- 语法：`GROUP_CONCAT(DISTINCT expression ORDER BY expression SEPARATOR sep)`

---

# SQL 代码

```
SELECT 
    sell_date,
    COUNT(DISTINCT product) AS num_sold,
    GROUP_CONCAT(
        DISTINCT product 
        ORDER BY product 
        SEPARATOR ',' 
    ) AS products
FROM 
    Activities
GROUP BY 
    sell_date
ORDER BY 
    sell_date;
```

### 知识点扩展 (Pair Programming Tips)

- **GROUP_CONCAT 的威力**:

- 这个函数在 MySQL 面试中非常高频，因为它解决了“列转行”再拼接的复杂问题。
- 默认的分隔符就是逗号 `,`，所以 `SEPARATOR ','` 其实可以省略，但写出来更清晰。
- **陷阱**：`GROUP_CONCAT` 有长度限制（默认 1024 字节）。如果拼接的字符串特别长（比如几千个产品），会被截断。可以通过设置 `group_concat_max_len` 参数来调大。

- **其他数据库的等价函数**:

- **PostgreSQL**: `STRING_AGG(DISTINCT product, ',' ORDER BY product)`
- **Oracle**: `LISTAGG(product, ',') WITHIN GROUP (ORDER BY product)` (Oracle 的去重比较麻烦，通常要先子查询去重)。
- **SQL Server**: `STRING_AGG(product, ',') WITHIN GROUP (ORDER BY product)`。