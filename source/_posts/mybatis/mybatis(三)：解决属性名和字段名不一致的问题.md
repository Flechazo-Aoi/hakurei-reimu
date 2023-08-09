---
title: mybatis(三)：解决属性名和字段名不一致的问题
categories: java
tags:
- mybatis
- 框架 
---

## 问题:

- 数据库中字段名和pojo中对应实体类的属性名不一致导致查出来的对应属性值为null
- 查询时,对应不一致的属性无法查出来,因为从数据库查到数据之后,会根据字段名创建一个user对象,(创建时是mybatis底层整合的,没有用类的set,get和有参构造方法,也就是没有用反射)如果字段名和类属性不一致,创建的类对象这个属性就相当于没有被赋值,也就是null
## 解决方法:

- 起别名:很麻烦,而且还需要我们知道数据库存储的数据

```mysql
select id,name,pwd as password from mybatis.user where id = #{id}
```
- resultMap 结果集映射
```xml
    <!--结果集映射-->
    <resultMap id="userMap" type="com.hanser.pojo.User">
        <!--column列，代表数据库中的字段名，property属性，代表实体类中的属性-->
        <!--建立需要重新映射的属性和字段的映射关系-->
        <result column="pwd" property="password"/>
    </resultMap>

    <!--通过id查找一个用户-->
    <select id="getUserById" resultMap="userMap">
        select *
        from mybatis.user
        where id = #{id}
    </select>
```