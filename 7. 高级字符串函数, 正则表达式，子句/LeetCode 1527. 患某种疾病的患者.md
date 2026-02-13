# 题目概述

**题目描述**：  
表 `Patients` 包含三列：

- `patient_id` (患者ID，主键，int)
- `patient_name` (患者姓名，varchar)
- `conditions` (疾病列表，varchar)：包含 0 个或多个疾病代码，以空格分隔。

要求编写一个 SQL 查询，查找患有 **I类糖尿病**（Type I Diabetes）的患者。I类糖尿病的代码总是以 `DIAB1` 开头。返回结果表，包含 `patient_id`, `patient_name`, `conditions`。

**示例**：  
输入：

|   |   |   |
|---|---|---|
|patient_id|patient_name|conditions|
|1|Daniel|YFEV COUGH|
|2|Alice||
|3|Bob|YFEV **DIAB1**00|
|4|George|ACNE **DIAB1**00|
|5|Alain|**DIAB2**01|

输出：  
用户 3 (`DIAB100` 在中间) 和 用户 4 (`DIAB100` 在开头) 符合条件。

---

### 解题思路

核心难点在于如何**精确匹配** `DIAB1` 开头的单词，同时避免误判（比如 `SADIAB100` 这种虽然包含 DIAB1 但不是以它开头的情况）。

我们需要处理两种情况：

1. **该疾病代码位于字符串开头**：比如 `"DIAB100 MYOP"`。

- 匹配模式：`DIAB1%`

2. **该疾病代码位于字符串中间或末尾**：比如 `"YFEV DIAB100"`。

- 匹配模式：`% DIAB1%` (注意前面有一个空格)

**错误思路**：

- `LIKE '%DIAB1%'`：❌ 错误。会匹配到 `SADIAB100`（前缀不是 DIAB1 的假数据）。

**正确思路**：  
使用 `OR` 连接上述两种情况，或者使用正则表达式。

---

### SQL 代码

**解法 1：使用 LIKE**

```sql
# Write your MySQL query statement below
# 找到conditions中包含DIAB1 前缀的子字符串的所有患者

SELECT 
    patient_id, 
    patient_name, 
    conditions
FROM 
    Patients
WHERE 
    conditions LIKE 'DIAB1%'      -- 情况1：开头就是
    OR 
    conditions LIKE '% DIAB1%';   -- 情况2：中间或结尾（前面必须有空格）
```

### 知识点扩展

- **单词边界**:

- 这道题考察的是对**字符串边界**的处理。
- 如果不加空格判断，很容易产生“误杀”或“漏杀”。
- `% DIAB1%` 中的空格非常关键，它确保了 `DIAB1` 是作为一个新单词的开始，而不是上一个单词的后缀。

- **性能考量**:

- `LIKE 'DIAB1%'` 可以走索引（最左前缀）。
- `LIKE '% DIAB1%'` 无法走索引（全表扫描）。
- 在真实业务场景中，如果 `conditions` 字段很长且查询频繁，建议将疾病代码拆分成独立的 Tag 表（一对多关系），而不是存成空格分隔的字符串，那样查询性能会高得多。