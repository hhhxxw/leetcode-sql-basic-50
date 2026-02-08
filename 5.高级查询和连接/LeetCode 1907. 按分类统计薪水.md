>这一开始我还在想🤔如何用case when 去进行判断，但是这样有一个问题，如果某一个种类没有，那怎么去进行group呢,所以这种思路不行

# 题目概述

**输入：**

一张名为 `Accounts` 的表，包含 `account_id` (账号) 和 `income` (收入)。

**需求：**

统计不同收入段的银行账户数量。具体的分类标准如下：

- **Low Salary**: 收入 strictly lower than (<) 20,000。
- **Average Salary**: 收入在 range [20,000, 50,000] 之间 (inclusive)。
- **High Salary**: 收入 strictly greater than (>) 50,000。

**关键约束：**

- 结果表必须包含所有三个类别。
- 如果某个类别中没有账户，则该类别的 `accounts_count` 必须返回 `0`。

# 解题思路

为了保证三个类别都出现在结果中，无论是否有数据，最稳健的方法是**分别计算三个类别的数量，然后将结果拼接起来**。

1. **查询 1**：硬编码字符串 `'Low Salary'`，统计 `income < 20000` 的行数。
2. **查询 2**：硬编码字符串 `'Average Salary'`，统计 `income >= 20000 AND income <= 50000` 的行数。
3. **查询 3**：硬编码字符串 `'High Salary'`，统计 `income > 50000` 的行数。
4. **合并**：使用 `UNION ALL` 将这三个独立的查询结果合并成一个结果集。

**为什么用** `**UNION ALL**` **而不是** `**UNION**`**？**`UNION` 会尝试去重，效率略低；而在这里我们可以确定三个查询生成的 `category` 字符串是不一样的，绝对不会重复，所以使用 `UNION ALL` 速度更快。

# SQL代码

```
/* 1. 统计 Low Salary */
SELECT
    'Low Salary' AS category,
    COUNT(*) AS accounts_count
FROM Accounts
WHERE income < 20000

UNION ALL

/* 2. 统计 Average Salary */
SELECT
    'Average Salary' AS category,
    COUNT(*) AS accounts_count
FROM Accounts
WHERE income >= 20000 AND income <= 50000

UNION ALL

/* 3. 统计 High Salary */
SELECT
    'High Salary' AS category,
    COUNT(*) AS accounts_count
FROM Accounts
WHERE income > 50000;
```