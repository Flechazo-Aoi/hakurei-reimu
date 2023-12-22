---
title: Mybatis的QueryWrapper
categories: 计算机基础
date: 2023-11-27 09:22:33
tags: 
- mybatis
---

## eq、ne

- eq：等于。`wrapper.eq("name", "hanser");`
- ne：不等于。`wrapper.ne("name", "hanser");`

## gt、ge、lt、le

- gt：大于。`wrapper.gt("age", 27);`
- ge：大于等于。`wrapper.ge("age", 27);`
- lt：小于。`wrapper.lt("age", 27);`
- le：小于等于。`wrapper.le("age", 27);`

## between、notBetween

- between：在值1和值2之间。`wrapper.between("age", 10, 20);`
- notBetween：不在值1和值2之间。`wrapper.notBetween("age", 10, 20);`

需要注意的是，`between`的含义包含边界值，而`notBetween`不包含边界值，也就是说上述例子的含义为，

- 找到年龄大于等于10，小于等于20的人
- 找到年龄小于10或者年龄大于20的人

## like、notLike、likeLeft、likeRight

- like："%值%"。`wrapper.like("name", "han");`
- notLike："%值%"。`wrapper.notLike("name", "han");`
- likeLeft："%值"。`wrapper.likeLeft("name", "ser");`
- likeRight："值%"。`wrapper.likeRight("name", "han");`

## isNull、isNotNull

- isNull：字段为null。`wrapper.isNull("name");`
- isNotNull：字段不为空。`wrapper.isNotNull("name");`

## in、notIn

- in：字段 IN (v0,v1,v2...)。`wrapper.in("age",18,19,20,21)`
- notIn：字段 NOTIN (v0,v1,v2...)。`wrapper.notIn("age",ageList)`

第一个参数为字段名，第二个参数可以输入可变参数，也可以输入一个集合

## or、and

- or：或者。`wrapper.gt("age", 20).or().eq("gender", 1)`
- and：和。`rapper.gt("age", 20).eq("gender", 1)`

两个条件连接默认是使用and连接，如果使用`or()`则表明两个条件使用or连接

## orderByAsc、orderByDesc

- orderByAsc：升序，ORDER BY 字段 ASC。`wrapper.orderByAsc("id")`
- orderByDesc：降序，ORDER BY 字段 DASC。`wrapper.orderByDesc("id")`

## inSql、notInSql（不常用）

- inSql：字段 IN ( sql语句 )。`wrapper.inSql("select id from employee where id < 10");`
- notInSql：字段 NOT IN ( sql语句 )。`wrapper.notInSql("select id from employee where id < 10");`

## exists、notExists（不常用）

- exists：拼接 EXISTS ( sql语句 )。`wrapper.exists("select name,gender from employee where id = 1")`
- notExists：拼接 NOT EXISTS ( sql语句 )。`wrapper.notExists("select name,gender from employee where id = 1")`