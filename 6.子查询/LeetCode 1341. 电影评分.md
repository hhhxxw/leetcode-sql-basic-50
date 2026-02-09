# 题目概述

**题目场景**：  
我们有三张表：`Movies` (电影信息), `Users` (用户信息), `MovieRating` (评分记录)。需要找出两个“最”：

1. **评论最多的人**：查找评论电影数量最多的用户名。如果有并列，按字典序取更小的那个。
2. **平均分最高的电影**：查找在 **2020年2月** 平均评分最高的电影名。如果有并列，按字典序取更小的那个。

**输出格式**：  
返回一张表，只有一列 `results`。

- 第一行是人名。
- 第二行是电影名。

---

# 解题思路

这道题其实是**两个完全独立的查询**，最后用 `UNION ALL` 拼在一起。

#### 第一部分：找评论最多的人

1. **关联表**：`MovieRating` JOIN `Users`。
2. **分组**：按 `user_id` 或 `name` 分组。
3. **统计**：`COUNT(*)` 计算评论数量。
4. **排序**：

- 先按 `COUNT(*)` **降序** (找最多的)。
- 再按 `name` **升序** (字典序小的在前)。

5. **取第一**：`LIMIT 1`。

#### 第二部分：找 2020年2月 分数最高的电影

1. **关联表**：`MovieRating` JOIN `Movies`。
2. **筛选**：`created_at` 在 `'2020-02-01'` 和 `'2020-02-29'` 之间（或者 `LEFT(created_at, 7) = '2020-02'`）。
3. **分组**：按 `movie_id` 或 `title` 分组。
4. **统计**：`AVG(rating)` 计算平均分。
5. **排序**：

- 先按 `AVG(rating)` **降序**。
- 再按 `title` **升序**。

6. **取第一**：`LIMIT 1`。

#### 拼接结果

使用 `UNION ALL` 将两个结果集上下拼接。

- _注意：这里不需要去重，所以用_ `UNION ALL` _比_ `UNION` _性能好（虽然结果只有两行，没差）。_

---

# SQL 代码

```sql
(
    -- Query 1: 评论最多的人
    SELECT u.name AS results
    FROM MovieRating mr
    LEFT JOIN Users u ON mr.user_id = u.user_id
    GROUP BY mr.user_id
    ORDER BY COUNT(*) DESC, u.name ASC
    LIMIT 1
)
UNION ALL
(
    -- Query 2: 2020年2月均分最高的电影
    SELECT m.title AS results
    FROM MovieRating mr
    LEFT JOIN Movies m ON mr.movie_id = m.movie_id
    WHERE mr.created_at LIKE '2020-02%'  -- 或者 BETWEEN '2020-02-01' AND '2020-02-29'
    GROUP BY mr.movie_id
    ORDER BY AVG(mr.rating) DESC, m.title ASC
    LIMIT 1
);
```

### 关键点总结

1. **括号的使用**：在 MySQL 中，如果 `UNION` 的子查询里包含 `ORDER BY` 和 `LIMIT`，**必须给子查询加括号**，否则语法报错或排序失效。
2. **字典序处理**：题目明确要求“并列时按字典序取小”，所以 `ORDER BY` 必须包含两列：主指标 DESC，名称 ASC。
3. **日期过滤**：`LIKE '2020-02%'` 是处理日期字符串最高效简便的方法之一（如果字段是 DATE/DATETIME 类型，索引也能生效）。