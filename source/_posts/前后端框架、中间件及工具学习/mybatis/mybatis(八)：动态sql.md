---
title: mybatis(八)：动态sql
categories: 前后端框架、中间件及工具学习
tags:
- mybatis
- 框架 
---

> 什么是动态SQL：动态SQL就是指根据不同的条件生成不同的SQL语句,摆脱拼接sql的苦。如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。 借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

## 环境搭建

### 创建blog表

```mysql
CREATE TABLE `blog`(
  `id` VARCHAR(50) NOT NULL COMMENT '博客id',
  `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
  `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
  `create_time` DATETIME NOT NULL COMMENT '创建时间',
  `views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8

```
### 创建实体Blog类

```java
package com.hanser.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Blog {
    private String id;
    private String title;
    private String author;
    private Date createTime;
    private int views;
}
```
除此之外,utils包下还有一个UUID生成工具类,可以生成一个唯一的不重复的随机的UUID

```java
package com.hanser.utils;

import java.util.UUID;

@SuppressWarnings("all")  //抑制所有警告
public class UUIDutils {
    //数据库主键自增会出现，最后一个人id是5，那么删除5之后，再次创建一个新的人，他的id是6而不是5
    //这个类就是生成一个唯一的不重复的随机的UUID
    public static String getId(){
        //UUID.randomUUID().toString()得到一个UUID，并转换成String类型
        //因为形式是........-....-....-....-............     所以用replaceAll("-","")把“-”都去掉
        return UUID.randomUUID().toString().replaceAll("-","");
    }

}
```

### 修改配置文件部分内容

除了常规的配置(别名,日志,注册配置文件)外,Blog的属性createTime与数据库的字段create_time不一致,这个不一致是二者命名规则不一致导致的,在setting中设置对应属性即可

```xml
<!--开启自动驼峰命名规则，因为oracle数据库不区分大小写，所以经典数据库命名是A_COLUMN,将其自动转换为aColumn形式-->
<setting name="mapUnderscoreToCamelCase" value="true"/>
```
## 动态sql

### if

java的if没啥区别

```xml
<!--用if实现按条件查询，能进入多个，只要它满足条件即可-->
<!--where标签可以智能的加上where，如果没有子条件成立就不会加where，如果是第一个子条件，会自动删除and，保证正常拼接-->
<select id="getBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <!--<if test="title != null">是判断条件.title是map的一个属性-->
        <if test="title != null">
            title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </where>
</select>
```
### choose

有时我们不想用到所有的条件语句,而只想从中选择一项,mybatis提供了choose语句,相当于java的switch,每次只会进入一种情况

```xml
    <!--用choose实现按条件查询,类似switch case，只会进入一个，且进入顺序就是排列的顺序,且至少设置一个条件-->
    <select id="getBlogChoose" parameterType="map" resultType="blog">
        select * from mybatis.blog
        <where>
            <choose>
                <when test="title != null">
                    and title = #{title}
                </when>
                <when test="author != null">
                    and author = #{author}
                </when>
                <otherwise>
                    and views = #{views}
                </otherwise>
            </choose>
        </where>
    </select>
```
### trim(where,set)

- where:where 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。
- 如果 where 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：
```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```
- set:set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）

### for/each

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）比如：

```xml
<!--select * from mybatis.blog where id in (1,2,3);
在对一个集合遍历时，通常是需要构建in语句时，可以用foreach标签
你可以设置item(集合项,是自己对这个集合元素取的名字)，index(索引)，collection(集合名)以及开头项(open)，结尾项(close)和每个元素之间的分隔符(separator)
select * from mybatis.blog where (id = 1 or id = 2 or id = 3);
-->
<select id="getBlogByID" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <foreach collection="idList" item="bid" open="(" close=")" separator="or">
            id = #{bid}
        </foreach>
    </where>
</select>
```
### SQL片段

有的时候，我们可能会将一些功能的部分抽取出来，方便复用

- 使用SQL标签抽取公共的部分
```xml
<!--最好基于单表来定义sql片段，其不要存在where标签-->
<sql id="if-title-author">
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
```
- 在需要使用的地方使用Include标签引用(`refid= `来进行选择)即可
```xml
<select id="getBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if-title-author"/>
    </where>
</select>
```