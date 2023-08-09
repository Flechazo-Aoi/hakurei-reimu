---
title: mybatis(一)：第一个mybatis程序及使用mybatis实现增删改查
categories: java
tags:
- mybatis
- 框架 
---

# 简介

## 什么是Mybatis

MyBatis是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis免除了几乎所有的JDBC代码以及设置参数和获取结果集的工作。 MyBatis可以通过简单的XML或注解来配置和映射原始类型、接口和 Java
POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

MyBatis 本是apache的一个开源项目iBatis, 2010个项目由apache software foundatic迁移google code，并且改名为MyBatis。2013年11月迁移到Github。

要使用mybatis，首先要导入mybatis依赖

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```

## 为什么需要Mybatis

- 方便
- 帮助程序猿将数据存入到数据库中。
- 传统的JDBC代码太复杂了。简化。框架。
- 不用Mybatis也可以。更容易上手。技术没有高低之分
- 优点 简单易学, 灵活, sql和代码的分离，提高了可维护性。 提供映射标签，支持对象与数据库的orm字段关系映射 提供对象关系映射标签，支持对象关系组建维护 提供xml标签，支持编写动态sql。

# 第一个mybatis程序

思路；搭建环境-->导入mybatis-->编写代码-->测试

## 搭建环境

- 创建数据库

```mysql
create database mybatis;

use mybatis;

create table user
(
    id   int primary key not null,
    name varchar(20) default null,
    pwd  varchar(20) default null
) default character set = utf8
```

- 插入数据

```mysql
insert into mybatis.user (id, name, pwd)
VALUES (1, 'hanser', 'hanser'),
       (2, '孙兴憨', 'hanser'),
       (3, '憨色', 'hanser')
```

- 新建项目 新建一个普通的maven项目,删除src目录,这样这个maven项目就相当于是一个父项目,在父项目下建module就可以新建和父项目一样环境的子项目

- 导入依赖 mybatis,mysql,junit的依赖

```xml

<dependencies>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

## 导入mybatis

- 编写mybatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?
                useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--注册配置文件，每一个Mapper.xml都要在mybatis的核心配置文件中注册-->
    <mappers>
        <mapper resource="com/hanser/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

- 编写mybatis工具类 因为每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。
  而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例

所以编写一个工具类来获取SqlSessionFactory 的实例,

```java
package com.hanser.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

//mybatis工具类
//SqlSessionFactory工厂类，用来生产SqlSession对象
public class MybatisUtils {

    //提高sqlSessionFactory的作用域,让下面的方法能取到SqlSessionFactory对象
    private static SqlSessionFactory sqlSessionFactory;

    //写一个静态代码块,这个类加载的时候,这段代码就会执行,就会得到SqlSessionFactory对象
    static {
        try {
            //使用mybatis的第一步，获取SqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //既然有了SqlSessionFactory，顾名思义我们就能从中获得SqlSession实例了
    //SqlSession完全包含了面向数据库执行sql命令所需的所有方法
    public static SqlSession getSession() {
        //openSession()方法里能设置一个boolean类型的的参数,默认为false,表示关闭自动提交功能
        return sqlSessionFactory.openSession();
    }

}

```

## 编写代码

第一步：MabstisUtils工具类

第二步：实体类-User

第三步：接口UserMapper

第四步：UserMapper.xml

- 实体类-User 属性名要和字段名匹配,构造方法,set get都不是刚需
- UserMapper 一个接口,代表你要实现哪些功能
- UserMapper.xml 接口的实现类,之前是UserMapperImpl,现在转为一个配置文件,
每一个配置文件都需要在核心配置文件mybatis-config.xml中

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个接口,要和Dao/mapper接口名一致,要用全限定名-->
<mapper namespace="org.mybatis.example.BlogMapper">

    <!--select查询语句-->
    <!--id对应接口的方法名，resultType是返回结果集的类型,List就用list里的类型
    parameterType,参数类型.当返回类型和参数类型为int或String时,可以省略-->
    <!--获取用户列表-->
    <select id="getUserList" resultType="com.hanser.pojo.User">
        select *
        from mybatis.user
    </select>
</mapper>
```

## 测试

编写一个测试类,使用junit测试

```java
//注意SqlSession对象不是线程安全的，因此是不能共享的，所以它的最佳的作用域是请求或方法作用域
//最好是每次收到请求，就可以打开一个 SqlSession，返回一个响应时关闭它
public class UserMapperTest {

  @Test
  public void getUserList() {
    //1、获取session对象
    SqlSession session = MybatisUtils.getSession();

    //2、获取Mapper对象
    UserMapper userMapper = session.getMapper(UserMapper.class);

    //3、调用Mapper对象的方法来得到结果
    List<User> userList = userMapper.getUserList();

    for (User user : userList) {
      System.out.println(user);
    }

    //一定要记得关闭
    session.close();

  }
}
```
注意:因为我们UserMapper.xml文件在dao包下,而编译时只有resources包下的配置文件会被解析,
所以存在Maven导出资源问题,我们需要在pom.xml加入这段代码来解决Maven导出资源问题
```xml
    <!--maven中约定大于配置，所以可能发生我们自己写的配置文件(特指不在resources文件夹下的中的配置文件)，无法被导出或生效的问题-->
    <!--解决方案:在build中配置recourse，来防止我们资源导出失败的问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>

```

##  拓展

涉及到的三种对象的作用域
![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304132052059.png)

# 使用mybatis实现增删改

## 具体实现

在原有上拓展即可,UserMapper加对应方法名,UserMapper.xml加对应的sql语句,测试类写测试

- UserMapper.xml
```xml
    <!--方法的参数可以用#{}来取,如果是一个类对象,那么可以#{}自动提取类的属性-->
    <!--插入一个用户,对象中的属性可以直接取出来 #{}里面可以直接取出传入对象的属性-->
    <insert id="insertUser" parameterType="com.hanser.pojo.User">
        insert into mybatis.user (id, name, pwd)
        values (#{id}, #{name}, #{pwd})
    </insert>

    <!--修改一个用户,对象中的属性可以直接取出来-->
    <update id="updateUser" parameterType="com.hanser.pojo.User">
        update mybatis.user
        set name=#{name},
            pwd=#{pwd}
        where id = #{id};
    </update>

    <!--删除一个用户,对象中的属性可以直接取出来-->
    <delete id="deleteUser" parameterType="int">
        delete
        from mybatis.user
        where id = #{id}
    </delete>

```
- 测试类
注意如果没有设置自动提交事务,增删改要手动提交事务,执行出问题要回滚

## 拓展

Map

- 假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map!
- Map的key为参数名称,value是参数的值,
- 使用时,xml中parameterType为map,#{}可以直接取出map中的值
###模糊查询
- sql注入:
```
<!-- sql注入,如果查询时输入不加限制，用户可以通过输入奇怪的值来达到目的
select * from mybatis.user where id = 1 or 1 = 1,这样会查出整张表内容-->
```
我们之前是使用sql拼接的方式实现的,而现在我们在xml中编写sql语句时有两种方式,
方法1:`select * from mybatis.user where name like #{name}`

- 这种方式允许用户输入通配符,就可能会出现sql注入问题

方法2 `select * from mybatis.user where name like "%"#{name}"%"`

- 这种方式在sql中将通配符规定死，不允许客户传入其他参数，就可以避免sql注入
