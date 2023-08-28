---
title: spring(五)：autowired
categories: 前后端框架、中间件及工具学习
tags:
- spring
- 框架 
---

- 自动装配是Spring满足bean依赖一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性
  

在Spring中有三种装配的方式：
- 在xml中显式的配置(使用依赖注入di)
- 在java中显式配置(省去xml文件，编写一个javaconfig类)
- 隐式的自动装配bean【重要】

## 测试

### 环境搭建:

- 创建一个实体类,一个人有俩宠物

```java
public class People {
    private Cat cat;
    private Dog dog;
    private String name;
}
```
- xml配置
```xml

<beans>
    <!--要现在spring容器注册 对象属性对应的类,才能在下面自动装填-->
    <bean id="cat" class="com.hanser.pojo.Cat"/>
    <bean id="dog" class="com.hanser.pojo.Dog"/>

    <!--
    bean的自动装填，注意到people中由setDog和setCat方法，所以考虑能不能让bean调用set方法利用dog和cat已有的bean自动对属性进行赋值
    1、autowire="byName"，会自动在容器上下文中查找，set方法名去掉set后首字母小写等于容器中已经创建的bean的id来自动set赋值,需要保证所有bean的id唯一
    2、autowire="byType"，会自动在容器上下文中查找，将自己属性类型相同的bean自动set装填，但使用时需保证需要装填的类型的class全局唯一
    -->

    <bean id="people" class="com.hanser.pojo.People" autowire="byName">
        <property name="name" value="hanser"/>
    </bean>
</beans>
```

### 使用注解实现自动装填

jdk1.5后支持注解，Spring2.5就支持注解了！

#### 要使用注解时,必须:

- 导入约束
``` 
xmlns:context="http://www.springframework.org/schema/context"
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
```
- 配置注解的支持
```xml
<!--开启注解的支持-->
<context:annotation-config/>
```
之后就可以使用注解了

#### @Autowired

直接在属性上使用即可！也可以在set方法上使用,代表这个set方法对应的属性要实现自动装填！
默认使用byType来寻找，如果属性的类有多个bean同类型，就会改用byName

```java
public class People {
    //如果显式定义了Autowired的required属性为false，说明这个对象可以为null，否则不允许为空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```
#### @Qualifier

如果环境比较复杂,无法使用byType和byName,我们可以使用`@Qualifier(value = " ")`去配合@Autowired的使用，指定一个唯一的bean对象注入。value代表特定对象的bean的id

```java
public class People {
    @Autowired
    @Qualifier(value = "cat1")
    private Cat cat;
}
```
#### @Resource

也是用来实现自动装填,@Resource默认通过byName的方式实现，如果找不到名字，则通过byType实现！如果两个都找不到的情况下，就报错！可以通过`@Resource(name = "")`这种方式加上属性来限定某个特定的bean

```java
public class People {
    @Resource(name = "dog2")
    private Dog dog;
}
```



