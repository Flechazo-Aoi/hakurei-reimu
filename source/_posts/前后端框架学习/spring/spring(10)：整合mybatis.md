---
title: spring(十)：整合mybatis
categories: 前后端框架学习
data: 2022-12-10  11:22:33
tags: 
- spring
- 框架 
---

## 方式一：mybatis-spring

### 导入相关的包

- Junit
- mybatis
- MySQL
- spring相关
- aop织入
- mybatis-spring 【new】

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
	<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.29</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>

    <!--spring操作数据库的话，还要一个spring-jdbc-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.2</version>
    </dependency>
</dependencies>
```

### 编写配置文件

1) 编写数据源配置
2) 创建sqlSessionFactory
3) 创建sqlSessionTemplete(模板类，其实就是我们之前的的sqlSession，而且他是线程安全的，所以都用它)
- spring-dao.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!--DataSource(数据源):使用spring的数据源替换Mybatis的配置
    我们这里使用Spring提供的jdbc,这样mybatis配置文件中对应的部分就可以删去了，
    相当于Spring提供数据源-->

    <!--可以直接写属性，或者写在一个配置文件db.properties里
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?
                useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>
    -->
    <!--引入外部配置文件-->
    <context:property-placeholder location="classpath:db.properties"/>
    <!--配置datasource-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <!--建议属性名使用user,使用username会获取系统用户名字,会冲突-->
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
    </bean>

    <!--用Spring实现sqlSessionFactory的创建,必须要配的是参数dataSource,其余都是可选项-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource"/>
        <!--还可以在这里绑定mybatis配置文件,这里面的属性包含了mybatis配置文件中所有能设置的值，
        也就是说mybatis-config.xml可以不再需要，但一般会给他剩下别名和设置,以此代表这个项目使用了mybatis-->
        <!--mybatis配置文件所在的位置-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--注册映射器-->
        <property name="mapperLocations" value="classpath:com/hanser/dao/*.xml"/>
    </bean>

    <!--用Spring实现SqlSessionTemplate的创建:就是我们使用的sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--SqlSessionTemplate类没有set方法，所以只能用构造器注入sqlSessionFactory-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
    
</beans>
```
这样我们就把sqlSessionFactory，sqlSession全部交给spring托管,MybatisUtils这个配置文件也不再需要

### 写一个UserMapperImpl实现类

那我们已经有了sqlSession对象,那么我们怎么执行dao层的类方法呢?我们当然可以像原来一样，在service层创建sqlSession对象，然后用这个sqlSession对象通过反射创建UserMapper接口对象，再调用它的方法，但是，service层其实并不需要sqlSession这个对象，他只关心一些业务相关的东西而spring只能托管类，所以我们可以在dao层创建一个UserMapperImpl实现类，在这个类里实现sqlSession的创建，这样各个类的分工更加明确,service层的类就更加纯粹

- UserMapperImpl实现类
```java
package com.hanser.dao;

import com.hanser.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

public class UserMapperImpl implements UserMapper{
    //在原来，我们所有操作都是用sqlSession来执行，而现在，我们使用sqlSessionTemplate(Template：模板)
    //sqlSessionTemplate是线程安全的
    
    //依旧是设置私有属性,然后用set方法来注入
    private SqlSessionTemplate sqlSession;
    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }

    @Override
    public User selectUserById(int id) {
        return sqlSession.getMapper(UserMapper.class).selectUserById(id);
    }

    @Override
    public int deleteUserById(int id) {
        return sqlSession.getMapper(UserMapper.class).deleteUserById(id);
    }

    @Override
    public int updateUser(User user) {
        return sqlSession.getMapper(UserMapper.class).updateUser(user);
    }

    @Override
    public int addUser(User user) {
        return sqlSession.getMapper(UserMapper.class).addUser(user);
    }
}
```
之后在spring配置文件中注册这个类,就可以在service层使用了

## 方式二：实现类继承SqlSessionDaoSupport来简化操作

mybatis-spring1.2.3版以上的才有这个，官方文档截图：
![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141017612.png)
一直到创建sqlSessionFactory这一步和上面都是一样的，之后让实现类继承SqlSessionDaoSupport，这样就不用手动注入sqlSessionTemplete，简化操作

```java
public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
    //继承了SqlSessionDaoSupport类之后，就可以直接调用getSqlSession()方法来得到SqlSession对象
    @Override
    public User selectUserById(int id) {
        return getSqlSession().getMapper(UserMapper.class).selectUserById(id);
    }
}
```
SqlSessionDaoSupport是一个类,里面有一个私有属性sqlSessionFactory,没有构造器,有set方法,所以采用set注入；同时配置bean时，不再需要配置sqlSession，因为类里有get方法，需要注入sqlSessionFactory,以此来得到sqlSession对象
```xml
<bean id="userMapper2" class="com.hanser.dao.UserMapperImpl2">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

> 注意：在Spring托管的SqlSession允许手动关闭，SqlSessionTemplate的close()方法是会抛异常的，因为它不允许直接调用该方法进行关闭，而是每次执行事务的时候自动关闭。SqlSessionTemplate是SqlSession接口的一个实现类

