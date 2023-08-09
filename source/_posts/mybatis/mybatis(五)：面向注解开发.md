---
title: mybatis(五)：面向注解开发
categories: java
tags:
- mybatis
- 框架 
---

# 使用注解开发

## 面向接口编程

大家之前都学过面向对象编程，也学习过接口，但在真正开发中，很多时候我们会选择面向接口编程

- 根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体实现，大家都遵守共同的标准，使得开发变得容易，规范性更好
- 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的，对系统设计人员来讲就不那么重要了；
- 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程。

### 对于接口的理解：

- 从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离。
- 接口的本身反映了系统设计人员对系统的抽象理解。
- 接口应有两类：
  - 第一类是对一个个体的抽象，它可对应为一个抽象体(abstract class);
  - 第二类是对一个个体某一方面的抽象，即形成一个抽象面(interface);
- 一个体有可能有多个抽象面。抽象体与抽象面是有区别的。

### 三个面向区别

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法.
- 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题,更多的体现就是对系统整体的架构

## 使用注解开发

- 注解在接口上实现

```java
@Select("select * from user")
List<User> getUserList();
```
- 因为使用注解后,就省去了Mapper.xml文件,所以注册映射要绑定到一个类class或者用包名
```xml
<!--  可以用通配符来把一个包下的文件全部绑定：<mapper resource="com/hanser/dao/*.xml"/>  -->
<mappers>
    <mapper class="com.hanser.dao.UserMapper"/>
</mappers>
```
## 注解的实现

- 本质：反射机制实现
- 底层：动态代理

动态代理在后续spring的面向切面编程时再详细说明

## Mybatis详细的执行流程

- Resources获取全局配置文件,对应工具类中的这两行

```java
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
```
- 实例化SqlSessionFactoryBuilder构造器：
```java
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
```
- 解析配置文件流XMLConfigBuilder(.build方法的底层实现)
- Configuration配置信息(.build方法的底层实现)
- .build方法创建一个SqlSessionFactory实例
- transactional事务管理(这个玩意存在于executor执行器中)
- 创建executor执行器(这个玩意存在于session对象中)
- 创建sqlSession
- 实现CRUD
- 看是否成功执行事务,不成功回滚到transactional事务管理,成功了就提交

## 通过注解实现CRUD

```java
package com.hanser.dao;

import com.hanser.pojo.User;
import org.apache.ibatis.annotations.*;

import java.util.HashMap;
import java.util.List;

/*
 * 注意，使用注解开发会让维护变得困难
 * 且只适用于一些简单的sql语句，较为复杂的多表查询就会变得很复杂
 * 且无法像xml中配置ResultMap结果集映射的方法来解决字段名和属性名不一致的情况
 * 使用注解，不用在dao层创建对应的xml文件
 * */

public interface UserMapper {

    @Select("select * from user")
    List<User> getUserList();

    /*关于@Param()注释
    * 基本类型和String类型的参数需要加上
    * 引用类型的参数不用加
    * 如果只有一个基本类型的话，可以忽略，但是建议加上,有多个参数必须加上
    * 我们在注解的sql中#{}取得名称的是@Param()设置的值
    * */
    //通过id查找用户
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id") int id);

    //插入用户
    @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{pwd})")
    int addUser(User user);

    //更新用户信息
    @Update("update user set name=#{name},pwd=#{pwd} where id = #{id}")
    int updateUser(User user);

    //删除一个用户
    @Delete("delete from user where id = #{id}")
    int deleteUserById(@Param("id")int id);


}

```

## 
