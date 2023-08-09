---
title: spring(一)：spring简介与IOC初探
categories: java
tags:
- spring
- 框架 
---

## 简介

- Spring：春天--->给软件行业带来了春天！
- 2002，首次推出了Spring框架的雏形：interface21框架！
- Spring框架即以interface21框架为基础，经过重新设计，并不断丰富其内涵，于2004年3月24日发布了1.0正式版。
- Rod Johnson，Spring Framework创始人，著名作者。很难想象Rod Johnson的学历，真的让好多人大吃一惊，他是悉尼大学的博士，然而他的专业不是计算机，而是音乐学。
- Spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架！
- SSH：Struct2 + Spring + Hibernate!
- SSM：SpringMVC + Spring + Mybatis!
- 依赖

```xml
<!--spring框架依赖支持-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
```

### 优点

- Spring是一个开源的免费的框架（容器）！
- Spring是一个轻量级的、非入侵式的框架！
- 两种核心思想,控制反转（IOC），面向切面编程（AOP）！
- 支持事务的处理，对框架整合的支持！

总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架！

### 组成

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304132245470.png)

### 拓展

现代化的Java开发，说白就是基于Spring的开发

spring的弊端：发展了太久之后，违背了原来的理念！配置十分繁琐，人称：“配置地狱！”

Spring Boot

- 一个快速开发的脚手架。
- 基于SpringBoot可以快速的开发单个微服务。
- 约定大于配置。

Spring Cloud

- SpringCloud是基于SpringBoot实现的。 因为现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC！承上启下的作用！

## IOC理论推导

1. UserDao 接口

```java
public interface UserDao {
    void getUser();
}
```

2. UserDaoImpl实现类

```java
public class UserDaoImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("通过默认方法得到用户");
    }
}
```

3. UserDaoMybatisImpl实现类

```java
public class UserDaoMybatisImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("通过mybatis得到用户");
    }
}
```

4. UserDaoMysqlImpl实现类

```java
public class UserDaoMysqlImpl implements UserDao {

    @Override
    public void getUser() {
        System.out.println("通过sql方法得到用户");
    }
}
```

5. UserService 业务接口

```java
public interface UserService {
    void getUser();
}
```

6. UserServiceImpl实现类

```java
public class UserServiceImpl implements UserService {
    // 因为service层要调用dao层的方法，所以要声明一个dao层的对象
    private UserDao userDao = new UserDaoImpl();

    @Override
    public void getUser() {
        //调用dao层的方法
        userDao.getUser();
    }
}
```

7. 测试类

```java
public class MyTest {
    public static void main(String[] args) {
        //用户实际调用的是业务层，dao层他们不需要接触！
        UserService userService = new UserServiceImpl();
        userServiceImpl.getUser();
    }
}
```

在我们之前的业务中，如果我们把调用上一层的接口对象显式的声明为某个实现类的话,如果需求要改变 那么我们要修改代码,就要去对应的底层,去修改这个显式声明的对象,如果代码量变大的话,这是很麻烦得 所以有了IOC(控制反转),由之前的显式定义`private UserDao userDao = new UserDaoImpl();`变为,程序想要什么,我们就给他传入什么

```java
private UserDao userDao;
//利用set进行动态实现值的注入！
public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}
```

- 之前，程序是主动创建对象！控制权在我们手上！
- 使用了set注入后，程序不再具有主动性，而是变成了被动接收对象！
- 这种思想，从本质上解决了问题，我们不用再去管理对象的创建了。系统的耦合性大大降低， 可以更加专注的在业务的实现上！这是IOC的原型

## IOC本质

**控制反转IoC（Inversion of Control），是一种设计思想，DI（依赖注入）是实现IoC的一种方法**

> 也有人认为DI只是IoC的另一种说法。没有IoC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，
> 对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，
Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection，DI）。**





