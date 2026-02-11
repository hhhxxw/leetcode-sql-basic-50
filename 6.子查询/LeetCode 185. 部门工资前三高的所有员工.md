# 题目概述
	
**题目场景**：  
有两张表：`Employee` (员工表) 和 `Department` (部门表)。

- `Employee`: id, name, salary, departmentId
- `Department`: id, name

**目标**：  
找出每个部门中 **工资最高的前三名** 员工。

- **并列情况**：如果有多个员工工资相同且都是前三，那么这些人都应该被算进去。例如，第一名有2人，第二名有1人，第三名有1人，这4个人都应该输出（因为第一名占用了名次，但不占用人数，或者说“工资数”是排名的依据）。

**输出**：  
`Department` (部门名), `Employee` (员工名), `Salary` (工资)。

---

# 解题思路

这道题的核心是 **分组排序** 和 **处理并列排名**。

#### 方法一：使用窗口函数 `DENSE_RANK()` (最推荐)

这是标准解法，几乎所有现代数据库都支持。

1. **关联表**：先把员工和部门连起来，拿到部门名字。
2. **打排名**：对每个部门内的员工按工资降序排名。

- **关键点**：必须用 `DENSE_RANK()` 而不是 `RANK()` 或 `ROW_NUMBER()`。
- `ROW_NUMBER()`: 1, 2, 3, 4 (并列时不并列，直接顺延) -> **错**。
- `RANK()`: 1, 1, 3, 4 (并列占名次，第3名变第3) -> **错**。
- `DENSE_RANK()`: 1, 1, 2, 3 (并列不占名次，第3名还是第2) -> **对**。

3. **筛选**：只取排名 `<= 3` 的记录。

#### 方法二：子查询计数 (老版本 MySQL 解法)

如果不能用窗口函数，思路是：对于每一个员工 `e1`，统计同一个部门里有多少人的工资比他高。

- 如果比他高的人数 `< 3`（即 0, 1, 2 个），说明他是前三名。
- 这里需要去重（`COUNT(DISTINCT salary)`），因为工资相同的人不算“比他高”。

---

# SQL 代码

#### 方案 1：窗口函数 `DENSE_RANK()` (推荐)

```sql
WITH RankedEmployee AS (
    SELECT 
        d.name AS Department,
        e.name AS Employee,
        e.salary AS Salary,
        -- 按部门分组，按工资降序，生成密集排名
        DENSE_RANK() OVER (
            PARTITION BY e.departmentId 
            ORDER BY e.salary DESC
        ) AS rk
    FROM Employee e
    JOIN Department d ON e.departmentId = d.id
)
SELECT Department, Employee, Salary
FROM RankedEmployee
WHERE rk <= 3;
```

#### 方案 2：相关子查询 (无窗口函数)

```sql
SELECT 
    d.name AS Department,
    e1.name AS Employee,
    e1.salary AS Salary
FROM Employee e1
JOIN Department d ON e1.departmentId = d.id
WHERE (
    -- 统计同部门里，工资比我高的人有多少个（去重）
    SELECT COUNT(DISTINCT e2.salary)
    FROM Employee e2
    WHERE e2.departmentId = e1.departmentId 
      AND e2.salary > e1.salary
) < 3  -- 比我高的人少于3个，那我就是前3名
ORDER BY d.name, e1.salary DESC;
```

### 关键点总结

1. **排名函数的选择**：一定要分清 `RANK` (跳跃排名) 和 `DENSE_RANK` (连续排名)。题目要求“前三高”，通常意味着如果有两个第一名，他们都是 No.1，下一个人是 No.2，所以选 `DENSE_RANK`。
2. **分区**：`PARTITION BY departmentId` 是必须的，因为我们要的是“每个部门内部”的排名。
3. **连接**：别忘了 `JOIN Department` 获取部门名称。