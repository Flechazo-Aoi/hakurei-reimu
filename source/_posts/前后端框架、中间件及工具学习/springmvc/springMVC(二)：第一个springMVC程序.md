---
title: springMVC(二)：第一个springMVC程序
categories: 前后端框架、中间件及工具学习
tags:
- 框架 
- springMVC
---

## 是什么是springMVC

SpringMVC是Spring框架中的一个分支，是基于Java实现MVC的轻量级Web框架

核心：Spring的web框架围绕DispatcherServlet [调度Servlet] 设计的。
![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141036483.png)
##2.1执行原理
![2-1](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141036682.png)
SpringMVC底层工作原理：和上面图一一对应,实线表示已经被MVC实现了,虚线表示我们自己要写

- 1.DispatcherServlet表示前置控制器,是整个SpringMVC的控制中心,用户发出请求,DispatcherServlet来接受并拦截请求
```xml
<!--配置DispatcherServlet:SpringMVC核心-->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <!--这是框架里自带的一个类-->
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--关联一个SpringMVC的resource配置文件,参数表示配置文件所在的位置-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMVC_servlet.xml</param-value>
    </init-param>
    <!--启动级别,服务器一启动,DispatcherServlet就启动-->
    <load-on-startup>1</load-on-startup>
</servlet>

<!--匹配那些请求
/ :只匹配请求，不包含所有的.jsp;/* :匹配所有的请求，包括jsp页面
/ 表示相对地址,相对于当前项目目录(服务器域名加站点)
http://localhost:8080/SpringMVC/hello
http://localhost:8080 -> 服务器的域名
SpringMVC -> 部署在服务器上的站点,就是配置tomcat时设置的
hello -> 控制器,我们自定义的名字-->
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```
- 2.HandlerMapping表示处理器映射,DispatcherServlet调用它(系统自动调用,封装在框架里),它能根据请求的url查找Handler(处理器)
- 3.HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器(Controller),这里查到为hello
```xml
    <!--2,3共同完成找到对应控制器的作用,就是去的容器里面找,/hello,/表示相对地址域名加站点名-->
    <!--BeanNameUrlHandlerMapping处理器：id就是绑定跳转的url=页面访问的网址-->
    <bean id="/hello" class="com.hanser.controller.HelloController"/>
```
- 4.HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等
- 5.HandlerAdapter表示处理器适配器,它按照特定的规则去执行Handler
- 6.Handler让具体的Controller(HelloController)执行
- 7.Controller通过与Service层交互获取具体的执行信息,并把执行信息返回给HandlerAdapter,如ModelAndView
```java
public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //1 创建modelAndView
        ModelAndView mv = new ModelAndView();
        //2 调用业务层，这里没有，就不写
        //3 封装对象，放在ModelAndView中
        mv.addObject("msg", "Hello SpringMvc");
        //4 封装要跳转的视图，放在放在ModelAndView中,会自动连接上配置文件配置的前缀和后缀(WEB-INF/jsp/hello.jsp)
        mv.setViewName("hello");
        return mv;
    }
}
```
- 8.HandlerAdapter将视图逻辑名(hello)和模型对象("msg", "Hello SpringMvc")传给DispatcherServlet
- 9.DispatcherServlet将视图逻辑名传给视图解析器ViewResolver来解析
```xml
<!--视图解析器ViewResolver：将后端处理好的数据和视图传给DispatchServlet，DS再交给ViewResolver先解析一遍，确认无误再传给前端
        必须熟悉，以后还要学模版引擎Thymeleaf/Freemarker...
        1 获取ModeAndView的数据
        2 解析ModeAndView的视图名字
        3 拼接视图名字，找到对应的视图 WEB-INF/jsp/hello.jsp
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!--后缀-->
        <property name="suffix" value=".jsp" />
    </bean>
```
- 10.视图解析器ViewResolver将解析后的视图名传给DispatcherServlet,这里就是加上前缀后缀,从hello变成WEB-INF/jsp/hello.jsp
- 11.DispatcherServlet根据解析后的视图名,调用具体的试图
- 12.最终视图传到前端呈现给用户

## 不使用注解情况下的开发

1 配置web.xml

- 完成DispatcherServlet，关联resource配置文件
```xml
<!--配置DispatcherServlet:SpringMVC核心-->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <!--这是框架里自带的一个类-->
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--关联一个SpringMVC的resource配置文件-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMvc_servlet.xml</param-value>
    </init-param>
    <!--启动级别-->
    <load-on-startup>1</load-on-startup>
</servlet>
        <!--匹配所有的请求：/ :只匹配请求，不包含所有的.jsp
                         /*:匹配所有的请求，包括jsp页面-->
<servlet-mapping>
<servlet-name>SpringMVC</servlet-name>
<url-pattern>/</url-pattern>
</servlet-mapping>
```
2.配置springMvc_servlet.xml
- 获得视图解析器、映射器、适配器，绑定跳转url
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--处理器映射器HandlerMapping:查找访问的url控制器,框架定好的类-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!--处理器适配器HandlerAdapter：controller将处理好的数据返回给HandlerAdapter,框架定好的类-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器ViewResolver：将后端处理好的数据和视图传给DispatchServlet，DS再交给ViewResolver先解析一遍，确认无误再传给前端
        必须熟悉，以后还要学模版引擎Thymeleaf/Freemarker...
        1 获取ModeAndView的数据
        2 解析ModeAndView的视图名字
        3 拼接视图名字，找到对应的视图 WEB-INF/jsp/hello.jsp
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!--后缀-->
        <property name="suffix" value=".jsp" />
    </bean>

    <!--BeanNameUrlHandlerMapping处理器：id就是绑定跳转的url=页面访问的网址-->
    <bean id="/hello" class="com.hanser.controller.HelloController"/>
</beans>
```
- 这样我们只需要写Controller层的类即可

3 HelloController实现Controller
- 注意实现的这个Controller是一个接口
```java
public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //1 创建modelAndView
        ModelAndView mv = new ModelAndView();
        //2 调用业务层，这里没有，就不写
        //3 封装对象，放在ModelAndView中
        mv.addObject("msg", "Hello SpringMvc");
        //4 封装要跳转的视图，放在放在ModelAndView中,会自动连接上配置文件配置的前缀和后缀(WEB-INF/jsp/hello.jsp)
        mv.setViewName("hello");
        return mv;
    }
}
```
4 将自己的类交给SpringIOC容器，注册bean
```xml
    <!--BeanNameUrlHandlerMapping处理器：id就是绑定跳转的url=页面访问的网址，必须带'/'，/表示相对地址(域名加站点名)-->
    <bean id="/hello" class="com.hanser.controller.HelloController"/>
```
5 写要跳转的界面(WEB-INF/jsp/hello.jsp)
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>hanser</title>
    <body>
    <h1>我是卡莎!</h1>
    <h2>${msg}</h2>
    </body>
</head>
</html>
```
### 可能出现的问题:404

- 查看控制台输出，看一下是不是缺少了什么jar包。
- 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！(问题就是父项目的依赖,子项目不能用)
--> 进入项目结构,进入Artifacts,进入对应项目,在WEB-INF目录下新建lib目录,把依赖都导进去

