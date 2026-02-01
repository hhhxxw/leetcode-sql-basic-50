## 题目概述

**题目背景：**  
需要计算每个用户的“确认率”。

- **输入表 1** `Signups`**：包含所有注册用户的** `user_id`
- **输入表 2** `Confirmations`：包含用户请求确认的消息记录，包含 `action` 字段（值为 `'confirmed'` 或 `'timeout'`）。

**确认率定义：**

确认率 = 该用户 confirmed 的次数 / 该用户请求确认的总次数

**特殊规则：**

1. 如果没有请求过确认消息的用户，确认率为 **0.00**。
2. 结果四舍五入保留 **2 位小数**。

---

## 解题思路

### 核心步骤

1. **确定主表（LEFT JOIN）：**

- 题目要求计算“每个用户”的确认率，哪怕他从未发送过请求。
- `Signups` 表包含了所有用户，而 `Confirmations` 表只包含发过请求的用户。
- **结论：** 必须使用 `Signups` 作为左表 (LEFT JOIN) 连接 `Confirmations`，确保没有发过请求的用户也能出现在结果中（此时右表字段为 NULL）。

2. **分组（GROUP BY）：**

- 我们需要以用户为单位输出结果，所以按 `s.user_id` 分组。

3. **计算逻辑（巧妙使用 AVG）：**

- **高阶技巧**：利用布尔值计算平均值。
- 在 MySQL 中，表达式 `action = 'confirmed'` 的结果是 **1 (True)** 或 **0 (False)**。
- 如果我们求这一列的 **平均值 (AVG)**，实际上就是：1 *（confirmed） + 0 *

4. **处理 NULL 值：**

- 对于那些在 `Signups` 中存在但在 `Confirmations` 中不存在的用户（没发过请求），LEFT JOIN 后 `action` 是 NULL。
- `AVG(NULL)` 的结果是 `NULL`。题目要求这种情况输出 `0.00`。
- **结论：** 使用 `IFNULL(..., 0)` 包裹结果。

5. **格式化：**

- 使用 `ROUND(..., 2)` 保留两位小数。

---

## 3. SQL 代码

```sql
SELECT 
    s.user_id, 
    ROUND(IFNULL(AVG(c.action = 'confirmed'), 0), 2) AS confirmation_rate
FROM 
    Signups s
LEFT JOIN 
    Confirmations c 
ON 
    s.user_id = c.user_id
GROUP BY 
    s.user_id;
```

**代码详解：**

1. `LEFT JOIN`：保证 `Signups` 中的 ID 100% 保留。如果没有匹配到 `Confirmations`，右边字段全为 NULL。
2. `c.action = 'confirmed'`：这是一个布尔表达式。如果为 'confirmed' 则为 1，否则为 0 (timeout)。
3. `AVG(...)`：计算上述 1 和 0 的平均值，自动忽略 NULL（如果不处理 IFNULL）。
4. `IFNULL(..., 0)`：如果一个用户完全没有记录（即 AVG 的结果是 NULL），强制转为 0。