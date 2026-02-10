# 题目概述

**题目场景**：  
有一张 `RequestAccepted` 表，记录了好友申请通过的记录。

- `requester_id`：发起好友申请的人的 ID。
- `accepter_id`：接受好友申请的人的 ID。
- `accept_date`：接受日期（本题不重要）。

**注意**：

- 如果是 A 加了 B，那么 A 和 B **互为好友**。
- 我们要找出**拥有好友数量最多**的那个人（或多个人）。
- 返回两列：`id` (用户ID) 和 `num` (好友总数)。
- 只需要返回一个结果（如果有并列第一，题目通常允许只返回任意一个，或者返回所有并列的，本题 LeetCode 的 Accept 规则通常只需 `LIMIT 1` 即可通过，但严谨的面试中可能需要处理并列）。

---

# 解题思路

这道题的坑在于：好友关系是**双向**的，但在表中只存了一次。  
比如 `A -> B`，在表中只有一行 `(requester_id=A, accepter_id=B)`。

- 对于 A 来说，B 是好友。
- 对于 B 来说，A 也是好友。

所以，我们需要把这两列数据合并（UNION ALL）起来统计。

#### 步骤 1：扁平化处理 (UNION ALL)

把 `requester_id` 和 `accepter_id` 两列数据，全都放在同一列里（比如叫 `id`）。

- `SELECT requester_id AS id FROM RequestAccepted`
- `UNION ALL`
- `SELECT accepter_id AS id FROM RequestAccepted`

这样，如果 A 加了 B，A 会出现一次（在 requester 里），B 也会出现一次（在 accepter 里）。每个人作为好友出现的次数，就是他总的好友数。

#### 步骤 2：分组统计 (GROUP BY & COUNT)

对合并后的 `id` 进行分组，计算 `COUNT(*)`。

#### 步骤 3：排序取最大 (ORDER BY & LIMIT)

按计数降序排列，取第一名。

---

# SQL 代码实现

```sql
WITH AllFriends AS (
    -- 1. 取出所有发起者
    SELECT requester_id AS id FROM RequestAccepted
    UNION ALL
    -- 2. 取出所有接受者
    SELECT accepter_id AS id FROM RequestAccepted
)
SELECT 
    id, 
    COUNT(*) AS num
FROM AllFriends
GROUP BY id
ORDER BY num DESC
LIMIT 1;
```