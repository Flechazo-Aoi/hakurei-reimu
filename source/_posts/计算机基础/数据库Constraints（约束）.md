---
title: 数据库Constraints（约束）
categories: 计算机基础
date: 2023-10-13  15:22:33
tags: 
- 数据库
- 随笔
---

## 什么是约束

SQL 约束用于规定表中的数据规则。如果存在违反约束的数据行为，行为会被约束终止。

约束可以在创建表时规定（通过 CREATE TABLE 语句）；或者在表创建之后规定（通过 ALTER TABLE 语句）。

## 语法

`CREATE TABLE + CONSTRAINT`

```sql
CREATE TABLE table_name
(
column_name1 data_type(size) constraint_name,
column_name2 data_type(size) constraint_name,
column_name3 data_type(size) constraint_name,
....
);
```

在SQL中，有以下6个约束

- `NOT NULL `- 指示某列不能存储 NULL 值。
- `UNIQUE` - 保证某列的每行必须有唯一的值，独一无二的值。
- `PRIMARY KEY`(主键) - `NOT NULL`和 `UNIQUE`的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。
- `FOREIGN KEY`(外键) - 保证一个表中的数据匹配另一个表中的值的参照完整性。
- `CHECK` - 保证列中的值符合指定的条件。
- `DEFAULT` - 规定没有给列赋值时的默认值。

## 6中约束介绍

### NOT  NULL约束

在默认的情况下，表的列 是接受 NULL 值的，但NOT NULL约束强制该列不接受 NULL值，强制字段始终包含非空值。如果不向字段添加值，就无法插入或更新记录。

```sql
-- 下面的 SQL 语句强制 "ID" 列、 "LastName" 列以及 "FirstName" 列不能为NULL。 --

CREATE TABLE Persons (
    ID          int NOT NULL,
    LastName    varchar(255) NOT NULL,
    FirstName   varchar(255) NOT NULL,
    Age         int
);


-- 在已创建的Persons表的 "Age" 字段中 添加 NOT NULL 约束 --

ALTER TABLE Persons
MODIFY Age int NOT NULL;


-- 在一个已创建的表的 "Age" 字段中删除 NOT NULL 约束 --

ALTER TABLE Persons
MODIFY Age int NULL;
```

### UNION 约束

- UNIQUE 约束唯一标识数据库表中的每条记录。
- UNIQUE 和 PRIMARY KEY 约束均为列或列集合提供了唯一性的保证。
- PRIMARY KEY 约束拥有自动定义的 UNIQUE 约束。
- 每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束。

```sql
CREATE TABLE Persons
(
P_Id        int NOT NULL UNIQUE,       -- 直接说明了是UNIQUE -- 
LastName    varchar(255) NOT NULL,
FirstName   varchar(255),
Address     varchar(255),
City        varchar(255)
CONSTRAINT  uc_PersonID UNIQUE (FirstName, LastName)   -- FirstName、LastName定义不重约束
);


-- 表Persons已经存在，需要在P_ID列创建UNIQUE约束 --
ALTER TABLE Persons
ADD UNIQUE (P_ID);


-- 表Persons已经存在，需要重命名UNIQE 约束，并定义多个列的约束 --
ALTER TABLE Persons
ADD CONSTRAINT uc_PersonID (P_ID, LastName);


-- 撤销UNIQUE约束 -- 
ALTER TABLE Persons
DROP CONSTRAINT uc_Person_ID;
```

### PRIMARY KEY (主键) 约束

- PRIMARY KEY约束 唯一标识数据库表中的每条记录。
- 主键必须包含唯一的值。
- 主键列不能包含 NULL 值。
- 每个表都有且只能有一个主键。

```sql
```

