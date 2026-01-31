### 题目概述

给你一张表 `Tweets`，常见字段：

- `tweet_id`：推文 id
- `content`：推文内容（字符串）

定义：如果一条推文的内容长度 **大于 15 个字符**，则该推文是“无效的”。

要求：找出所有无效推文的 `tweet_id`。

### 解题思路

1. 用字符串长度函数计算 `content` 的字符数
2. 过滤出长度 `> 15` 的行
3. 只返回 `tweet_id`

注意：不同 SQL 方言长度函数通常是 `LENGTH()`（MySQL）或 `CHAR_LENGTH()`（更严格按字符算）。

这里LENGTH和CHAR_LENGTH有什么区别？

- LENGTH(str) ：返回字节长度
- CHAR_LENGTH(str) ：返回字符个数

**如果是英文字符串没什么问题，但如果是非英文如中文字符串“你好”，LENGTH是6（字节），CHAR_LENGTH是2（字符），所以这里推荐使用CHAR_LENGTH**

### SQL代码

```sql
select tweet_id  from Tweets where CHAR_LENGTH(content) > 15;
```