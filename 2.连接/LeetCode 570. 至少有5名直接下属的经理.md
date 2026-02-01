## 题目概述

要从 `Employee` 表中找出那些至少拥有 **5 名直接下属** 的经理，并返回他们的姓名。

表结构：`Employee`

|   |   |   |
|---|---|---|
|Column Name|Type|Description|
|id|int|主键，员工的唯一标识|
|name|varchar|员工姓名|
|department|varchar|部门名称|
|managerId|int|该员工的经理 ID|

**示例数据：**

为了方便理解，可以把数据想象成一个简单的组织架构图。

|   |   |   |   |
|---|---|---|---|
|id|name|department|managerId|
|101|John|A|null|
|102|Dan|A|101|
|103|James|A|101|
|104|Amy|A|101|
|105|Anne|A|101|
|106|Ron|A|101|

**解析：**

- 在这个例子中，`id` 为 102, 103, 104, 105, 106 的员工，他们的 `managerId` 都是 **101**。
- 这意味着 **John (id: 101)** 有 5 个直接下属。
- **输出：** John

---

## 解题思路

### 核心逻辑

需要做两件事：

1. **数人头**：统计每个 `managerId` 在表中出现的次数（即该经理有多少下属）。
2. **找名字**：根据统计结果，找出次数大于等于 5 的那个 `managerId`，然后去查这个 ID 对应的 `name`。

思路

1. **第一步（里层）：** 只看 `managerId` 列，分组并统计。找出那些出现次数 的 ID。
2. **第二步（外层）：** 在主表中，查找 `id` 在刚才那个名单里的员工，输出他们的名字。

使用HAVING去对分组之后的数据进行过滤

---

## MySQL 代码

```sql
SELECT name 
FROM Employee 
WHERE id IN (
    -- 1. 先筛选出下属人数 >= 5 的经理 ID
    SELECT managerId 
    FROM Employee 
    GROUP BY managerId 
    HAVING COUNT(*) >= 5
);
```