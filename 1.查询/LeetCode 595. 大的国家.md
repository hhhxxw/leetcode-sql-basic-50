# 题目概述

给你一张表 `World`，包含（典型字段）：

- `name`：国家名
- `continent`：洲
- `area`：面积
- `population`：人口
- `gdp`：GDP

要求：找出“大的国家”，并返回这些国家的 `name`、`population`、`area`。

**“大的国家”定义：**

- `area >= 3000000`（面积至少 300 万） **或**
- `population >= 25000000`（人口至少 2500 万）

本题本质是一个 SQL 过滤查询题。

# 解题思路

1. 目标列：只需要输出 `name, population, area`
2. 过滤条件：满足任一条件即可，所以用 `OR`
3. 直接写 `SELECT ... FROM ... WHERE ...` 即可，无需 join、分组或排序（题目没要求排序）

逻辑就是：

- 选出世界表中，面积达标 **或** 人口达标的行。

# SQL代码

```sql
select name, population, area from World where  area >= 3000000 or population >= 25000000;
```