---
title: spring(四)：di
categories: 前后端框架学习
data: 2022-12-07  11:22:33
tags: 
- spring
- 框架 
---

**di：依赖注入**

- 依赖：bean对象的创建依赖于容器
- 注入：bean对象中的所有属性，由容器来注入

## 构造器注入

前面已经介绍过，参考spring(三)：配置文件配置

## set方式注入【重点】

### 环境搭建

- 一个包含多种类型属性的类

```java
package com.hanser.pojo;

import java.util.*;

@Data   // lombok的一个注解，用来自动生成get，set，toString，hashCode方法
public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbies;
    private Map<String, String> card;
    private Set<String> games;
    private Properties info;
    private String wife;
}

```

- 属性注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--依赖注入:
        依赖:bean对象的创建依赖于容器
        注入:bean对象中所有的属性,由容器注入
    -->
    <!--set注入,容器使用无参构造方法用set方法注入-->


    <!--在容器中声明address的引用,下面才能ref得到address-->
    <bean id="address" class="com.hanser.pojo.Address">
        <property name="address" value="大连"/>
    </bean>

    <bean id="student" class="com.hanser.pojo.Student">
        <!--第一种,普通值(基本类型和String),直接用value-->
        <property name="name" value="憨色"/>
        <!--第二种,bean注入,ref-->
        <property name="address" ref="address"/>
        <!--第三种,数组注入,用array标签-->
        <property name="books">
            <array>
                <value>博丽灵梦</value>
                <value>魂魄妖梦</value>
                <value>赫卡提亚</value>
                <value>帕秋莉</value>
            </array>
        </property>
        <!--第四种,list注入,用list标签-->
        <property name="hobbies">
            <list>
                <value>无人区</value>
                <value>去剪海的日子</value>
                <value>主播女孩重度依赖</value>
            </list>
        </property>
        <!--第五种,map注入,用map标签-->
        <property name="card">
            <map>
                <entry key="han" value="雷姆"/>
                <entry key="ser" value="拉姆"/>
                <entry key="c" value="艾米莉亚"/>
            </map>
        </property>
        <!--第六种,set注入,用set标签-->
        <property name="games">
            <set>
                <value>Muse Dash</value>
                <value>Apex</value>
                <value>DokiDoki</value>
            </set>
        </property>
        <!--第七种,null注入,用null标签-->
        <property name="wife">
            <null/>
        </property>
        <!--第八种,Properties注入,用props标签-->
        <property name="info">
            <props>
                <prop key="游戏">Muse Dash</prop>
                <prop key="学号">1234567</prop>
                <prop key="姓名">hanser</prop>
            </props>
        </property>
    </bean>

</beans>
```

## 拓展

我们可以使用**p命名空间**和**c命名空间**进行注入

- 使用前要引入xml约束

```
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

- p命名空间:p(property),通过set注入注入

```xml

<bean id="user1" class="com.hanser.pojo.User" p:age="18" p:name="hanser" scope="prototype"/>
```

- c命名空间:c(constructor-args),通过构造器注入

```xml

<bean id="user2" class="com.hanser.pojo.User" c:age="28" c:name="憨色"/>
```

## bean作用域

- 单例模式(Spring默认机制),无论调用多少次getBean方法，因为内存中只有这一个bean，所以取出来的都是一个对象

```xml
<bean id="user1" class="com.hanser.pojo.User" scope="singleton"/>
```

- 原型模式：每次从容器中get的时候，都会产生一个新对象！

```xml
<bean id="user1" class="com.hanser.pojo.User" scope="prototype"/>
```

- 其余的request、session、application、这些只能在web开发中使用到



