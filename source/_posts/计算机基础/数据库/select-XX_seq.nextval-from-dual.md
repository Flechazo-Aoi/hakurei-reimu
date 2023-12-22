---
title: select XX_seq.nextval from dual
categories: 计算机基础
date: 2023-10-07  15:22:33
tags: 
- 数据库
- 随笔
- oracle
---

j在业务中有一个需求，要求每次新增一条记录时，要在数据库创建一个对应的表，并且表名后缀为GROUPX，其中X为四位编号，从0001开始，每次创建都加一，这里需要考虑并发创建的情况，解决方案是oracle队列。

## oracle队列

是oracle提供的用于产生一系列唯一数字的数据库对象。

- 自动提供唯一的数值
- 共享对象
- 将序列值装入内存可以提高访问效率
- 主要用于提供主键值

### 创建队列

首先当前用户要有创建序列的权限 `create sequence` 或 `create any sequence`

**创建一个队列：**

```sql
CREATE SEQUENCE sequence 	 # 创建序列名称
       [INCREMENT BY n]  	 # 递增的序列值是n 如果n是正数就递增,如果是负数就递减 默认是1
       [START WITH n]   	 # 开始的值,递增默认是minvalue 递减是maxvalue
       [{MAXVALUE n | NOMAXVALUE}] 	# 最大值
       [{MINVALUE n | NOMINVALUE}] 	# 最小值
       [{CYCLE | NOCYCLE}] 		# 循环/不循环
       [{CACHE n | NOCACHE}];	# 分配并存入到内存中
```

```sql
# 创建一个队列，开始值为1、最大值为3、步长为1
Create sequence seqTemp increment by 1 start with 1 maxvalue 3 minvalue 1   
```

**获取队列的值：**

- `NEXTVAL `返回序列中下一个有效的值，任何用户都可以引用
- `CURRVAL` 中存放序列的当前值
- `NEXTVAL `应在 `CURRVAL `之前指定 ，二者应同时有效

```sql
# 先nextval 后 currval，否则报错
Select seqEmp.nextval  from dual;   # 1
Select seqEmp.currval  from dual;   # 1
```

**修改序列的注意事项：**

- 必须是序列的拥有者或对序列有`ALTER`权限
- 只有将来的序列值会被改变（未被取出来的）
-  改变序列的初始值只能通过删除序列之后重建序列的方法实现

```sql
Alter sequence seqTemp maxvalue 5;
```

**删除序列：**

- 使用DROP SEQUENCE 语句删除序列

- 删除之后，序列不能再次被引用

```sql
DROP SEQUENCE seqTemp
```

回到上述提到的业务场景，表名有了，下一步就是要在后端实现数据库表的创建，只需要在后端执行SQL语句来创建表、生成索引等等即可
