---
title: UNION / UNION ALL
categories: 计算机基础
date: 2023-11-09  15:22:33
tags: 
- 数据库
- 随笔
---

UNION 操作符用于合并两个或多个 SELECT 语句的结果集。

请注意，UNION 内部的 SELECT 语句必须拥有**相同数量的列**。列也必须拥有**相似的数据类型**。同时，每条 SELECT 语句中的列的**顺序必须相同**。

```sql
-- 默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。 --
SELECT column_name(s) FROM table_name1
UNION
SELECT column_name(s) FROM table_name2

-- 默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。 --
SELECT column_name(s) FROM table_name1
UNION ALL
SELECT column_name(s) FROM table_name2
```

另外，UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。如下面的方法查询结果的字段名为id和name

```sql
SELECT ID id,NAME name
FROM table1
UNION
SELECT ID id1,NAME name1
FROM table2
```

