# 题目概述

**题目描述**：  
表 `Users` 包含两列：

- `user_id` (int, 主键)
- `name` (varchar)
- `mail` (varchar)

要求编写一个 SQL 查询，查找拥有**有效电子邮件**的用户。  
**有效电子邮件**的定义：

1. 必须以 **字母** 开头。
2. 必须以 `@leetcode.com` 结尾。
3. 在 `@` 之前的部分（前缀），只能包含：

- **字母** (a-z, A-Z)
- **数字** (0-9)
- **下划线** `_`
- **句点** `.`
- **横杠** `-`

**示例**：

- `winston@leetcode.com` -> ✅ 有效
- `jonathanisgreat` -> ❌ 无效（没@）
- `bella-@leetcode.com` -> ✅ 有效
- `sally.come@leetcode.com` -> ✅ 有效
- `quincy_2020@leetcode.com` -> ✅ 有效
- `david69@gmail.com` -> ❌ 无效（域名不对）
- `david98@leetcode.com` -> ✅ 有效
- `.sharon@leetcode.com` -> ❌ 无效（以点开头）

---

# 解题思路

这道题考察的是 **正则表达式 (Regular Expression)** 在 SQL 中的应用。虽然用 `LIKE` 也可以勉强拼凑（极其复杂且不严谨），但正则表达式是解决此类模式匹配问题的最佳工具。

我们需要构建一个正则来匹配上述规则：

1. **开头**：`^[a-zA-Z]`

- `^` 表示字符串开始。
- `[a-zA-Z]` 表示必须是字母。

2. **中间部分**：`[a-zA-Z0-9_.-]*`

- 允许出现的字符集合：字母、数字、下划线、点、横杠。
- `*` 表示出现 0 次或多次。
- 注意：在正则中 `.` 和 `-` 是特殊字符，有时需要转义（但在字符集 `[]` 内通常不需要，除非位置特殊）。为了保险起见，可以写成 `[a-zA-Z0-9_.-]`。

3. **结尾**：`@leetcode\\.com$`

- `@` 匹配 @ 符号。
- `leetcode` 匹配 leetcode。
- `\\.com`：这里需要转义 `.`，因为在正则中 `.` 代表任意字符。如果不转义，`leetcodeacom` 也会被匹配。
- `$` 表示字符串结束。

**合并后的正则**：  
`^[a-zA-Z][a-zA-Z0-9_.-]*@leetcode\\.com$`

这里还要要求最后域是小写所以再加一个条件`mail like BINARY "%@leetcode.com"`

---

# SQL 代码

```
# Write your MySQL query statement below
select
    *
from Users
where 
 mail  REGEXP '^[a-z][a-z0-9_.-]*@leetcode\\.com$'  and mail like BINARY "%@leetcode.com";
```

### 知识点扩展 (Pair Programming Tips)

- **MySQL 的转义符**:

- 在 MySQL 的字符串中，反斜杠 `\` 本身也是转义符。
- 正则需要 `\.` 来表示点，但写在 SQL 字符串里就要变成 `\\.`（第一个 `\` 转义第二个 `\`，让正则引擎收到 `\.`）。

- **不同数据库的正则**:

- **MySQL**: `REGEXP` 或 `RLIKE`。
- **PostgreSQL**: `~` (区分大小写) 或 `~*` (不区分)。
- **Oracle**: `REGEXP_LIKE(mail, 'pattern')`。

- **LIKE vs REGEXP**:

- 如果只是简单的“以什么开头”，用 `LIKE 'abc%'` 性能更好（走索引）。
- 如果是复杂的“只能包含某些字符”，必须用正则。