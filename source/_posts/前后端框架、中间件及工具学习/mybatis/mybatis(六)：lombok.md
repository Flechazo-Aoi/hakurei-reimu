---
title: mybatis(六)：lombok
categories: 前后端框架、中间件及工具学习
tags:
- mybatis
- 框架 
---



## lombok简介

一款Java开发插件，使得Java开发者可以通过其定义的一些注解来消除业务工程中冗长和繁琐的代码，尤其对于简单的Java模型对象(POJO)。在开发环境中使用Lombok插件后，Java开发人员可以节省重复构建，诸如hashCode和equals这样的方法以及各种业务对象模型的accessor和ToString等方法的大量时间。对于这些方法，它能够在编译源代码期间自动帮我们生成这些方法，并且没有如反射那样降低程序的性能。

## 使用步骤：

- 在IDEA中下载lombok插件

file -->settings --> plugins

![image-20230413213810764](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304132138932.png)

- 导入依赖的jar包

```xml
<!--Lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>
```
- 之后在实体类上直接加入注解即可

## 常用注解

- @Data:生成无参构造，get、set、toString、hashCode,equals函数
- @AllArgsConstructor：生成有参构造函数会把无参构造给覆盖
- @NoArgsConstructor：生成无参构造函数
- @EqualsAndHashCode：生成equals和hashCode函数
- @ToString：生成toString函数
- @Getter:在对应属性上加上,表示生成该属性的getter方法,在类上加,表示所有属性都生成getter方法

## 优缺点

### 优点

- 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
- 让代码变得简洁，不用过多的去关注相应的方法
- 属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

### 缺点

- 不支持多种参数构造器的重载
- 虽然省去了手动创建getter/set比er方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度
