### 题目描述

**表名：** `Views`

|   |   |   |
|---|---|---|
|Column Name|Type|含义|
|article_id|int|文章的 ID|
|author_id|int|文章作者的 ID|
|viewer_id|int|浏览该文章的人的 ID|
|view_date|date|浏览日期|

**需求：**  
请编写一条 SQL 查询，找出所有**浏览过自己文章**的作者。

- 结果按照 `id` 升序排列（即从小到大）。
- 结果中的 `id` 必须是唯一的（去重）。
- 注意：结果列名应为 `id`。

**示例数据逻辑：**  
如果 `author_id` 为 4 的人，浏览了 `viewer_id` 为 4 的记录，说明他看自己的文章了。我们需要把这个 4 选出来。

---

### 解题思路

- 作者自己浏览自己文章”对应条件：author_id = viewer_id
- 只需要返回作者 id：SELECT author_id AS id
- 因为同一个作者可能多次浏览/多篇文章触发多行记录，需要 去重：DISTINCT
- 题目要求按 id 升序：ORDER BY id

### SQL 代码

```
SELECT DISTINCT author_id AS id
FROM Views
WHERE author_id = viewer_id
ORDER BY id ASC;
```