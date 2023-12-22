---
title: sql语句的执行顺序以及流程
categories: 计算机基础
date: 2023-09-27 15:22:33
tags: 
- 数据库
- sql
---

本文参考https://blog.csdn.net/weixin_44404350/article/details/116861936

一条Select语句完整的执行顺序：

## from 

from子句组装来自不同数据源的数据+(ON过滤器)或(JOIN 添加外部行）；

FROM子句执行顺序为从后往前、从右到左。+(ON过滤器)或(JOIN 添加外部行）

## where

where子句常用的查询条件如下所示：

| 查询条件          | 谓词                                    |
| ----------------- | --------------------------------------- |
| 比较              | =，<，>，<=，>=，!=,<>，!>，!<          |
| 确定范围          | between...and...，not  between...and... |
| 确定集合          | in，not in                              |
| 字符匹配          | like，not like                          |
| 空值              | is null，is not null                    |
| 逻辑运算/多重条件 | and，or，not                            |

## group by

group by 子句将数据划分为多个分组；

把结果集按照某一列或者多列进行分组（值相等的一组）

```sql
select gender,count(name) from student group by gender;

select gender,count(name),sum(class_id) from student group by gender;
```

## 聚集函数

使用聚集函数进行运算，注意：

- 聚集函数只能出现在 `select` 后面或者 `having` 后面。不能放到WHERE子句中
- 当聚集函数遇见空值的时候，除了`count()`外其余都跳过不进行处理。

| 函数                            | 解释                                             |
| ------------------------------- | ------------------------------------------------ |
| count (*)                       | 统计元素个数                                     |
| count ([distinct \| all]  列名) | 统计一列中值的个数                               |
| sum ([distinct \| all]  列名)   | 统计这列值的总和，这列的数据类型必须是数值型的   |
| avg ([distinct \| all]  列名)   | 统计这列值的平均值，这列的数据类型必须是数值型的 |
| max ([distinct \| all]  列名)   | 统计这列值的最大值                               |
| min ([distinct \| all]  列名)   | 统计这列值的最小值                               |

## having

使用 having 子句筛选分组；

如果分组后还要进行筛选 那么就需要having语句(有having,必须有group by)

```sql
select gender,count(name),sum(class_id) from student group by gender having gender= '女';
```

## 计算所有的表达式；

## select 

这其中也包括使用 `DISTINCT` 去重，窗口函数是 `SELECT` 语句里，而 `SELECT` 是在 `WHERE` 和 `GROUP BY` 之后。

## UNION连接

UNION连接的两个SELECT查询语句，会重复执行步骤1~7，产生两个虚拟表1，UNION会将这些记录合并到新的虚拟表2中

## order by

使用 order by 对结果集进行排序。

```sql
select * from student order by Sage asc; 查询结果按 Sage的结果升序排列 
select * from student order by Sage desc;查询结果按 Sage的结果降序排列
```

## Limit

以sid升序排序，显示前5行

```sql
select * from student order by sid desc limit 5;
```

