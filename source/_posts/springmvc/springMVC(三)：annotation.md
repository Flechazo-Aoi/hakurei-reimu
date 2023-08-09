---
title: springMVC(三)：annotation
categories: java
tags:
- spring
- 框架 
- springMVC
---

## 整体步骤

- 新建一个web项目
- 导入相关jar包
- 编写web.xml , 注册DispatcherServlet
- 编写springmvc配置文件
- 接下来就是去创建对应的控制类 , controller
- 最后完善前端视图和controller之间的对应
- 测试运行调试.

使用springMVC必须配置的三大件:处理器映射器、处理器适配器、视图解析器,
- 前两个开启注解驱动即可,最后一个要手动配置

## web.xml

这个配置文件没变,和之前一样

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动顺序,数字越小,启动越早-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
## springmvc-servlet.xml

- 这个文件中需要添加注解和mvc的约束,并且处理器映射器和处理器适配器已经用注解自动配置了,现在只需要手动配置视图解析器就可以了
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--设置要扫描的包,这个包下的注解才会生效,由IOC容器统一管理-->
    <context:component-scan base-package="com.hanser.controller"/>
    <!--使用注解自动加载处理器映射器和处理器适配器,不用再手动配置-->
    <mvc:annotation-driven/>
    <!--让springMVC不处理静态资源,比如.css,.js等不进视图解析器-->
    <mvc:default-servlet-handler/>

    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```
## Controller层对象

- 我们不再需要去实现Controller接口,而是直接加@Controller注解来声明处理器注册进spring容器
- 一个类可以整合多个方法,每个方法代表一个业务,
- 每个方法的返回值代表这个业务请求转发的逻辑视图
```java
//@Controller就是把这个类注册进容器中,相当于实现了Controller接口
@Controller
public class HelloController {

    //@RequestMapping代表访问地址,
    @RequestMapping("/hello")
    public String hello(Model model){
        //相当于之前的mv.addAttribute
        model.addAttribute("msg","Hello World");
        //返回值就是请求转发的逻辑视图,默认是使用请求转发实现的
        return "test";
    }
}
```

## 用到的注解

- @Controller:声明这个类已被容器托管
@Controller就是把这个类注册进容器中,相当于实现了Controller接口
- @RequestMapping("/hello"):代表访问的地址
可以写在类上和写在一个方法上,如果都写了,那么类上的相当于一级地址,方法上的相当于二级地址,但是一般不推荐在类上写,因为维护困难

