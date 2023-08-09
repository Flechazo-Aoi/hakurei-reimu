---
title: spring(三)：配置文件配置
categories: java
tags:
- spring
- 框架 
---

### bean标签

IOC容器创建对象的方式：

- 使用无参构造创建对象，默认 (用无参构造创建一个对象,然后调用它的set方法,实现属性的注入)
- 使用有参构造创建对象。

```xml
 <!--IOC默认的是调用无参构造,设置属性需要用到set方法,所以如果没有无参构造或set方法会报错
        可以自己实现有参构造
        1.<constructor-arg index="0" value="hanser"/>,index是属性的下标
        2.<constructor-arg type="java.lang.String" value="hanser" /> 
        type是属性的类型,不建议使用,如果有多个相同类型的参数会不方便
        3.<constructor-arg name="name" value="hanser"/> 直接通过参数名
        -->
<bean id="user" class="com.hanser.pojo.User">
    <constructor-arg name="name" value="hanser"/>
</bean>
```

在配置文件加载的时候，容器中管理的对象就已经初始化了

### 别名alias

```xml
<!--别名,添加了别名之后,我们getBean中参数可以使用原来名字,也可以使用别名-->
<alias name="user" alias="userNew"/>
```
### bean配置

```xml
<!--
    id : bean的唯一标识,相当于给原有类取个名
    class : bean对象的全限定名
    name : 给id取个别名,可以取多个别名,别名之间用","或者";"或者" "分割
    property ： 对象的属性
		其中name表示实体类中的属性名，value(基本数据类型和String)或ref(引用Spring容器中创建好的bean)代表属性的值
-->
<bean class="com.hanser.pojo.UserT" id="userT" name="userT2" >
    <property name="name" value="憨色"/>
</bean>
```
### import

一般用于团队开发使用，它可以将多个配置文件，导入合并为一个。假设，现在项目中有多个人开发，这三个人负责不同的类开发，不同的类需要注册在不同的bean中，我们可以利用import将所有人的beans.xml合并为一个总的。使用的时候，直接使用总的配置就可以了。

- bean1.xml
- bean2.xml
- bean3.xml
```xml
<!--import用于团队开发,可以一人一个xml,一个大的xml用import标签合并-->
<import resource="bean1.xml"/>
<import resource="bean2.xml"/>
<import resource="bean3.xml"/>
```



