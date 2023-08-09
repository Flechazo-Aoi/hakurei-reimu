---
title: springMVC(一)：初识springMVC
categories: java
tags:
- spring
- 框架 
- springMVC
---

## 什么是MVC

- Model:数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为）， 不过现在一般都分离开来：Value Object（数据Dao） 和
  服务层（行为Service）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。
- View:负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西
- Controller（调度员）： 接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图， 由视图负责展示。也就是说控制器做了个调度员的工作。

最常用的MVC：（Model）JavaBean +（view） Jsp +（Controller） Servlet

## Model1时代

- 分为：视图层V和模型层M；由视图层的view来控制分发数据并展示给用户
- 缺点：JSP职责不单一，过重，不便于维护
  ![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141035108.png)

## Model2时代（MVC延续至今）

流程：分为了Controller,Model,View

- 用户发请求
- Servlet接收请求数据，并调用对应的业务逻辑方法
- 业务处理完毕，返回更新后的数据给servlet
- servlet转向到JSP，由JSP来渲染页面
- 响应给前端更新后的页面
  ![img_1](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141035393.png)

## 回顾servlet
- 1.创建maven项目,选择空的maven项目,然后右键添加项目框架支持,这样可以选择版本4.0
- 2.建包,com.hanser.servlet,然后创建一个类HelloServlet,并继承HttpServlet
```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取前端参数
        String method = req.getParameter("method");
        if(method.equals("add")){
            req.setAttribute("msg","执行了add方法");
        }
        if(method.equals("delete")){
            req.setAttribute("msg","执行了delete方法");
        }
        //2.调用业务层处理参数,并返回结果
        //3.将结果携带并进行试图重定向(resp.sendRedirect("绝对路径或相对路径"))
        // 或转发(req.getRequestDispatcher("只能是相对路径[/...,第一个/表示当前web目录]").forward(req,resp);)
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

- 3.写了servlet就别忘了在web.xml下注册
```xml
<servlet>
  <servlet-name>hello</servlet-name>
  <servlet-class>com.hanser.servlet.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>hello</servlet-name>
<!--就是这个servlet的地址,/代表Tomcat配置的url-->
<url-pattern>/hello</url-pattern>
</servlet-mapping>

```
- 4.创建响应页面:/WEB-INF/jsp/test.jsp
```xml
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
  <h1>hello world</h1>
  <h1>${msg}</h1>
</head>
<body>
</body>
</html>
```
5.在index.jsp写一个表单来传入参数"method",
不配置的话要传入参数就要用问号携带:http://localhost:8080/hello?method=add
```xml
<form action="/hello" method="get">
  <input type="text" name="method">
  <input type="submit">
</form>
```
- 6.配置Tomcat环境,运行

