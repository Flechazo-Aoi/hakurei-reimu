---
title: springMVC(七)：Interceptor
categories: 前后端框架学习
data: 2022-12-18  11:22:33
tags: 
- springMVC
- 框架 
---

## 概述

- SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能。
- **过滤器与拦截器的区别**：拦截器是AOP思想的具体应用。

**过滤器：**

- servlet规范中的一部分，任何java web工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器：**

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器Controller方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的

## 实现简单的拦截器

- 想要实现拦截器，必须实现HandlerInterceptor 接口。
- 配置好环境，确保环境正确
- 编写一个类MyInterceptor实现HandlerInterceptor 接口

```java
public class MyInterceptor implements HandlerInterceptor {
    //处理前进行拦截,只有这个函数有返回值，true表示放行，可以继续往下执行，false表示拦截，不能再继续往下执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("====================处理前===================");
        return true;
    }
    //后面两个一般作为拦截日志
    //处理后拦截
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("====================处理后===================");
    }
    //清理
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("====================清理===================");
    }
}

```
- 在springMVC的配置文件中配置拦截器

编写了拦截器就一定要在拦截器中配置，配置使用了AOP思想，相当于容器给我们定义了一个切面
```xml
<!--配置拦截器-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--/** 包括路径及其子路径-->
        <!--/admin/* 拦截的是/admin/add等等这种admin下的所有一级目录, /admin/add/user不会被拦截-->
        <!--/admin/** 拦截的是/admin/下的所有-->
        <mvc:mapping path="/**"/>
        <!--bean配置的就是拦截器的那个类-->
        <bean class="com.hanser.config.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

## 实现登录拦截

首先搭建环境，确保环境无误后，开始进行开发

- 1.欢迎界面index.jsp，设置界面跳转(进入首页和进入登陆界面)
```html
<h1><a href="${pageContext.request.contextPath}/user/main" methods="post">进入首页</a> </h1>
<h1><a href="${pageContext.request.contextPath}/user/toLogin" methods="post" >登录</a> </h1>
```
- 2.登陆界面WEB-INF/jsp/login.jsp
```html
<form action="${pageContext.request.contextPath}/user/login" method="post">
    用户名：<input type="text" name="username">
    <br/>
    密码：<input type="password" name="password">
    <br/>
    <input type="submit" name="提交">
</form>
```
- 3.首页WEB-INF/jsp/main.jsp
```html
<h1>
    我是首页
    <span>${username}</span>
    <a href="/user/logOut">注销</a>
</h1>
```
- 4.后端Controller层的实现
```java
@Controller
@RequestMapping("/user")
public class LogInController {

    //从login.jsp获取用户名和密码，存入session，并跳转到首页
    @RequestMapping("/login")
    public String login(String username, String password, Model model, HttpSession session){
        System.out.println(username);
        if(!username.equals("")){
            session.setAttribute("username",username);
            model.addAttribute("username",username);
        }
        return "main";
    }
    //跳转到登陆界面
    @RequestMapping("/toLogin")
    public String toLogin(){
        return "login";
    }
    //跳转到首页main
    @RequestMapping("/main")
    public String toMain(){
        return "main";
    }
}
```

这样，我们能从欢迎页点首页直接进入，也可以点登录，登录之后进入，但是我们要实现，必须登录，没有登陆不能进入首页
实现登录拦截，就需要设置拦截器了，通常是采用session来进行判断，登录时，向session中存入一个值，在拦截器中判断
session中这个值是否为空
```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        System.out.println("进入拦截器");
        if(session.getAttribute("username")!=null){
            return true;
        }
        if(request.getRequestURI().contains("gin")){
            return true;
        }
        
        request.getRequestDispatcher("/WEB-INF/jsp/error.jsp").forward(request,response);
        return false;

    }
}

```



