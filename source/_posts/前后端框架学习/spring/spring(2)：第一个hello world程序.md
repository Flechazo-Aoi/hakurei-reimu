---
title: spring(二)：第一个hello world程序
categories: 前后端框架学习
date: 2022-12-06  11:22:33
tags: 
- spring
- 框架 
---

## Hello Spring

### 新建一个实体类

```java
public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}

```
### 编写xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--使用spring容器来创建对象,在spring中这些都称为bean(组件)
    之前创建对象都是类型 变量名 = new 类型();
    Hello hello = new Hello()
    现在我们把这个行为交给Spring来进行,
    IOC是一种编程思想,由主动的编程变为被动的接受,使用set方法依赖注入来实现控制反转
    -->
    <!--
    bean标签
    id 是创建的这个bean的名字
    class 是对应的类
    property标签
    name表示实体类中的属性名
    属性的值的设置有两种方式value:基本的数据类型和String;ref:引用Spring容器中创建好的bean-->
    
    -->
    <bean id="hello" class="com.hanser.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

</beans>
```
### 测试

```java
public class MyTest {
    public static void main(String[] args) {
        //获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在Spring中管理了,我们需要使用,直接取里面取出来就可以了,由容器通过反射创建
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello);
    }
}

```
## 思考
- Hello对象是谁创建的？   -->Spring
- Hello对象的属性是怎么设置的？ -->由Spring容器通过依赖注入(DI)设置的
这个过程就叫控制反转：
- 控制：谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象是由Spring来创建的。
- 反转：程序本身不创建对象，而变成被动的接收对象。
- 依赖注入：就是利用set方法来进行注入的。
- IOC是一种编程思想，由主动的编程变成被动的接收。

**OK，到了现在，我们彻底不用在程序中去改动了，要实现不同的操作，只需要在xml配置文件中进行修改，
所谓的IOC，一句话搞定：对象由Spring来创建，管理，装配！**

