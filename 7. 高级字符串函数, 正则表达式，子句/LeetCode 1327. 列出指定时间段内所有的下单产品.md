# 题目概述

题目描述 ：表 Products 包含产品信息 ( product_id , product_name , product_category )。  
表 Orders 包含订单信息 ( product_id , order_date , unit )。

要求编写一个 SQL 查询，查找在 2020年2月 这个时间段内， 总下单数量至少为 100 的产品。  
返回结果包含 product_name 和 unit (该产品在该月的总销量)。

示例 ：

- 产品 A 在 2月1日 卖了 60个，2月15日 卖了 50个 -> 总量 110 >= 100 -> 输出。
- 产品 B 在 2月1日 卖了 30个，3月1日 卖了 200个 -> 2月只有 30 < 100 -> 不输出。

# 解题思路

1. 连接表 (JOIN) ：

- 我们需要 Products 表里的名字 ( product_name ) 和 Orders 表里的数量 ( unit )。
- 所以第一步是用 JOIN 把这两张表连起来： Products p JOIN Orders o ON p.product_id = o.product_id

2. 筛选时间范围 (WHERE) ：

- 题目只关心 2020年2月 的数据。
- 您的写法 order_date >= '2020-02-01' AND order_date <= '2020-02-29' 是非常标准的 范围查询 ，这种写法最大的优点是 可以利用 order_date 上的索引 （如果有的话）。
- 备选写法 ： order_date LIKE '2020-02%' 或 LEFT(order_date, 7) = '2020-02' （但这两种写法可能无法利用索引，性能较差）。

3. 分组聚合与过滤 (GROUP BY + HAVING) ：

- 既然要算每个产品的“总销量”，就需要按产品分组： GROUP BY product_name 。
- 计算总销量： SUM(unit) 。
- 过滤条件：只保留销量 >= 100 的。这是针对 分组后 的结果进行筛选，所以必须用 HAVING SUM(unit) >= 100 ，而不能写在 WHERE 里。

# SQL 代码

```
# Write your MySQL query statement below
select
    p.product_name, sum(unit) as unit
from Products p
join Orders o 
on p.product_id = o.product_id
where order_date >= '2020-02-01' and order_date <= '2020-02-29'
group by p.product_id,p.product_name
having sum(unit) >= 100;
```