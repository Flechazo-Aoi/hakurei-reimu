---
title: Select 1 from dual
categories: 随笔
tags: 
  - orcal
  - 数据库
---

## Oracal里的`select 1 from dual`

今天在看公司代码的时候，发现有这一句SQL：

```sql
select 1 from dual
```

然后觉得有点奇怪，数据库里面都没有创建这个dual表，这个表是从何而来呢？

首先，公司用的是Oracle数据库，关于Oracle数据库中的dual表，官方文档说明（[The DUAL Table](https://docs.oracle.com/cd/E11882_01/server.112/e40540/datadict.htm#CNCPT1210)）：

`DUAL`是一个在数据字典里的很小的表，Oracle数据库和用户写的程序可以引用它来保证一个已知的结果。当一个值(比如当前date和time）有且仅需返回一次的时候，这个dual表还是很管用的。所有数据库用户都可以访问`DUAL`。

`DUAL`是一个随着Oracle数据库创建数据字典时自动创建的表。虽然`DUAL`在用户`SYS`模式下，但是还是可以被所有用户访问的。它有一列，`DUMMY`，定义为`VARCHAR2(1)`，和包含一行数据，值为`X`。

对于用`SELECT`计算一个常量表达式来说，从`DUAL`选择是比较好用的。因为`DUAL`只有一行，所以常量只会返回一次。或者，你可以从任意一个表中选择常量、伪列和表达式，但是这个值将返回多次，次数和表的行数一样多。

```sql
SQL> select * from dual;
DUMMY
-----
X
```

好的，现在我们知道了dual这个表是长什么样了，也知道为什么会用这个表了。划重点：**当一个值必须返回，且只返回一次，可以从dual表选择返回。**

我看了一下项目代码，这句SQL是传给数据库连接池验证连接的，这样就很合理了：不需要返回太多的值，但是有必须有返回，选择从dual返回再正确不过了。

## mysql里的`select 1 from dual`

`SELECT` can also be used to retrieve rows computed without reference to any table.
`SELECT`也可以在没有引用任何表，用来检索行。比如：

```sql
mysql> SELECT 1 + 1;
    -> 2
```

在没有引用表的情况下，你可以指定`DUAL`为一个虚拟表名。比如：

```sql
mysql> SELECT 1 + 1 FROM DUAL;
    -> 2
```

`DUAL`单纯是为那些要求所有`SELECT`语句应该有`FROM`或者其他子句的人们提供便利。MySQL可能忽略这个子句。因为即使没有表引用，MySQL也不要求`FROM DUAL`。

并且，在MySQL中使用dual表并不总是对的：

```sql
mysql> select 1 from dual;
3013 - Unknown table ****.dual
```

所以其实MySQL就直接`SELECT`就行，不用加上`from dual`