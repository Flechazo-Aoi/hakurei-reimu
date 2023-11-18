---
title: 
categories: 计算机基础
date: 2023-10-13  15:22:33
tags: 
- 数据库
- 随笔
---

# 数据库Constraints（约束）

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

## 6种约束介绍

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
-- 在 "Persons" 表创建时在 "P_Id" 列上创建 PRIMARY KEY 约束 --
CREATE TABLE Persons(
P_Id        int NOT NULL PRIMARY KEY,  -- 主键定义 -- 
LastName    varchar(255) NOT NULL,
FirstName   varchar(255),
Address     varchar(255),
City        varchar(255)
);


-- 在建表的同时命名 PRIMARY KEY 约束，并定义多个列的 PRIMARY KEY 约束 --
CREATE TABLE Persons
(
P_Id        int NOT NULL,
LastName    varchar(255) NOT NULL,
FirstName   varchar(255),
Address     varchar(255),
City        varchar(255),
CONSTRAINT  pk_PersonID PRIMARY KEY (P_Id,LastName)
);

-- 表Persons 已经被创建，需在 "P_Id" 列创建 PRIMARY KEY 约束 --
ALTER TABLE Persons
ADD PRIMARY KEY (P_Id);

-- 需要命名 PRIMARY KEY 约束，并定义多个列的 PRIMARY KEY 约束 --
ALTER TABLE Persons
ADD CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName);

-- 撤销 PRIMARY KEY 约束 --
ALTER TABLE Persons
DROP CONSTRAINT pk_PersonID;
```

```
注意事项：使用 ALTER TABLE 语句添加主键的时候，必须把主键列声明为不包含 NULL 值（在表首次创建时）。
```

### FOREIGN KEY (外键约束)

- 一个表中的 `FOREIGN KEY` 指向另一个表中的 `UNIQUE KEY`。
- `FOREIGN KEY` 约束用于预防破坏表之间连接的行为。
- `FOREIGN KEY` 约束能防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一。
- 在创建外键约束时，必须先创建外键约束所依赖的表，并且该列为该表的主键

```sql
-- 在 "Orders" 表创建时在 "P_Id" 列上创建 FOREIGN KEY 约束 --
CREATE TABLE Orders
(
O_Id    int NOT NULL PRIMARY KEY,
OrderNo int NOT NULL,
P_Id    int FOREIGN KEY REFERENCES Persons(P_Id) -- 外键定义 -- 
); 

-- 已经创建"Orders"表，如果需要在 "P_Id" 列创建 FOREIGN KEY 约束 --

ALTER TABLE Orders
ADD FOREIGN KEY (P_Id) REFERENCES Persons(P_Id); -- 外键定义 -- 

--  撤销 FOREIGN KEY 约束 --
ALTER TABLE Orders
DROP CONSTRAINT fk_PerOrders
```

### CHECK 约束

- CHECK 约束用于限制列中的值的范围。
- 如果对单个列定义CHECK约束，那么该列只允许存在特定的值。
- 如果对一个表定义CHECK约束，那么此约束会基于行中其他列的值在特定的列中对值进行限制。

```sql
-- 为P_Id增加约束：值必须大于0 --
CREATE TABLE Persons
(
P_Id        int             NOT NULL CHECK (P_Id > 0), -- check定义 --
LastName    varchar(255)    NOT NULL,
FirstName   varchar(255),
Address     varchar(255),
City        varchar(255)
);

-- 命名 CHECK 约束，并定义多个列的 CHECK 约束 --
CREATE TABLE Persons
(
P_Id        int NOT NULL,
LastName    varchar(255)    NOT NULL,
FirstName   varchar(255),
Address     varchar(255),
City        varchar(255),
CONSTRAINT  chk_Person CHECK (P_Id > 0 AND City = 'QingDao')
);

-- Person表已创建，在 "P_Id" 列创建 CHECK 约束 --
ALTER TABLE Persons
ADD CHECK (P_Id > 0); 

-- 命名 CHECK 约束，并定义多个列的 CHECK 约束 --
ALTER TABLE Persons
ADD CHECK chk_Person CHECK (P_Id > 0 AND City = 'Sandnes');

-- 撤销CHECK约束 -- 
ALTER TABLE Persons
DROP CONSTRAINT chk_Person;
```

### DEFAULT 约束

DEFAULT 约束用于向列中插入默认值。如果没有规定其他的值，那么会将默认值添加到所有的新记录。

```sql
-- 创建"Persons" 表时，在 "City" 列上创建 DEFAULT 约束 --
CREATE TABLE Persons
(
    P_Id        int NOT NULL,
    LastName    varchar(255)    NOT NULL,
    FirstName   varchar(255),
    Address     varchar(255),
    City        varchar(255)    DEFAULT 'QingDao' -- DEFAULT约束定义 -- 
);

-- Persons表已经被创建，在 "City" 列创建 DEFAULT 约束 --
ALTER TABLE Person
MODIFY City DEFAULT 'QingDao'; 

-- 撤销CHECK约束 --
ALTER TABLE Persons
ALTER COLUMN City DROP DEFAULT;
```

# UNION  /  UNION  ALL

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

另外，UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。
