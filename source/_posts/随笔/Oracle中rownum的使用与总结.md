---
title: Oracle中rownum的使用与总结
categories: 随笔
tags: 
- oracle
- 随笔
---

本文参考https://blog.csdn.net/qq_45427600/article/details/113990657

## rownum

- 伪列，顾名思义，是数据库自己创建出的字段
- 伴随着结果集的过程而生成的字段

比如`select * from user where rownum < 5;`或者`select rownum rown, * from user where rownum < 5;`就会查出伪列小于5的结果。

在执行这个sql的过程中，数据库会对查询出来的结果进行一一判断，先判断第一个结果，若满足where的条件，生成第一条结果然后加上他的伪列，伪列的默认值是1，从1开始，依次递增，如果第一条不满足，则丢弃。

所以产生一条规则：rownum不支持`>`，`>=`，`between and`，只支持`<`，`<=`等。尽管使用前者不会报错，但返回数据为空，因为根本无法满足where条件

```
如果是 where > 3
那么取回第1条数据的rownum为1，不满足，就舍弃这条记录。
看下一条，然后取第2条数据的rownum还是为1，还是不满足，再舍弃。
以此类推，最终舍弃了所有的数据，这就是所谓不支持的原因。
```

## 分页查询

rownum多用于分页查询,如查询第二页数据，每页只出现俩条数据：

```sql
select *
from (
         select rownum as rown, user.*
         from user
         where rownum < 5
     ) t1
where t1.rown > 2;
```

**在写分页的sql语句时，对于rownum关键字要注意几点问题（拿上面的sql举例）：**

1. 在进行单条查询时，rownum可以不用出现在select之后。如：`select, * from user where rownum < 5`
2. where子句后的rownum不能加表名，加表名代表是这个数据表的字段，而rownum字段是数据库自己生成的伪列，如上述例子中，子查询的`rownum`不能使用`user.rownum`代替，而父查询使用了`t1.rown`是因为把子查询生成了一个表t1，而rown是t1表的一个字段。
3. 不能在select子句使用rownum的别名作为where条件后的rownum。但是子句中的order by可以使用rownum的别名。这是因为一条SQL语句的执行顺序为from -> where -> group by -> 聚集函数 -> having -> 计算所有的表达式 -> selcet的字段+（DISTINCT去重） -> UNION连接的两个SELECT查询语句 -> order by -> limit

```xml
<select id="queryMapForPage" resultType="com.topscomm.basic.BasicLowerMap" parameterType="map">
	SELECT * FROM 
		(SELECT tt.*,ROWNUM as rowno from 
			(select * FROM table
		 <if test="whereSql != null and whereSql != ''">
			<![CDATA[
				WHERE ${whereSql}
		    ]]>		 
		 </if>
		<![CDATA[
				ORDER BY ${sidx} ${sord},id asc
			) tt
			where ROWNUM<=#{begincount}+#{pagesize}) table_alias
		where table_alias.rowno >= #{begincount}+1				
		]]>	
	</select>
```

