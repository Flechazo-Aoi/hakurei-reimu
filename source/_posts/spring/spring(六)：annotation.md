---
title: spring(六)：annotation
categories: java
tags:
- spring
- 框架 
---

在Spring4之后，要使用注解开发，必须要保证aop的包已经导入

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304132244173.png)

使用注解需要导入约束，配置注解的支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--指定要扫描的包,这个包下的注解才会生效-->
    <context:component-scan base-package="com.hanser"/>
    <!--开启注解的支持-->
    <context:annotation-config/>
</beans>
```

## 常用注解

### 用于装配bean

- @Component ，放在类上，相当于在xml中用bean配置，说明这个类被Spring管理了
- 我们在web开发中，会按照MVC三层架构分层，不同层的不同类的组件注解不同，但都是@Conponent的衍生注解
- @Repository，dao层类的组件注解
- @Controller，Controller层的组件注解
- @Service，Service层的组件注解

### 用于属性注入

@Value: 卸载属性或者set方法上 @Value("hanser")也就是属性注入，可以给简单类型的属性注入值，

### 用于自动装配

- @Autowired和@Qualifier(value = "xxx")
- @Nullable 字段标记了了这个注解，说明这个字段可以为null;
- @Resource：自动装配通过名字，类型。

### 作用域

@Scope("prototype")，在类的上方加上作用域，括号内是要设定的作用域

## 小结

xml与注解：

- xml更加万能，适用于任何场合！维护简单方便
- 注解不是自己类使用不了，维护相对复杂！

xml与注解最佳实践：

- xml用来管理bean；
- 注解只负责完成属性的注入；
- 我们在使用的过程中，只需要注意一个问题：要让注解生效，就需要开启注解的支持





