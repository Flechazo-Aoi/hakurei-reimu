---
title: springMVC(四)：Controller层
categories: java
tags:
- spring
- 框架 
- springMVC
---

## 控制器Controller

- 控制器复杂提供访问应用程序的行为，通常通过接口定义(实现Controller接口)或注解定义两种方法实现。
- 控制器负责解析用户的请求并将其转换为一个模型。
- 在Spring MVC中一个控制器类可以包含多个方法
- 在Spring MVC中，对于Controller的配置方式有很多种

## 实现Controller接口

Controller是一个接口，在org.springframework.web.servlet.mvc包下，接口中只有一个方法；

```java
//实现该接口的类,就说明这是一个控制器,获得了控制器功能
public class HelloController1 implements Controller {
    //处理请求且返回一个模型与视图对象
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg", "hello");
        mv.setViewName("test");
        return mv;
    }
}
```

### 说明

- 实现接口Controller定义控制器是较老的办法
- 没有显式的配置处理器映射器和处理器适配器也能正常执行,因为能启动默认配置
- 缺点是：一个控制器中只有一个方法，如果要多个方法则需要定义多个Controller；定义的方式比较麻烦；

## 使用注解@Controller

- @Controller注解类型用于声明Spring类的实例是一个控制器（在讲IOC时还提到了另外3个注解）
- Spring可以使用扫描机制来找到应用程序中所有基于注解的控制器类，为了保证Spring能找到你的控制器，需要在配置文件中声明组件扫描。

```xml
<!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
<context:component-scan base-package="com.hanser.controller"/>
```

- 增加一个HelloController2类，使用注解实现；

```java
//@Controller注解的类会自动添加到Spring上下文中,被spring托管
//其中所有方法,如果返回值为String,并且返回值的逻辑视图有具体的页面可以跳转,都会被视图解析器解析
@Controller
public class HelloController2 {
    //映射访问路径
    @RequestMapping("/hello2")
    public String hello(Model model) {
        //Spring MVC会自动实例化一个Model对象用于向视图中传值 
        model.addAttribute("msg", "你好");
        //返回视图的逻辑位置
        return "test";
    }
}
```

## 提示:

tomcat项目中,

- 如果改了前端,那么刷新一下就行,Update classes and resources
- 如果改了java代码,那么redeploy就行
- 如果改了配置文件,那么就得重新发布tomcat,restart server

## RequestMapping

@RequestMapping

- @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。
- 可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

## RestFul 风格

### 1.概念

RestFul风格是一种资源定位以及资源操作的风格.不是标准也不是协议,只是一种风格.基于这个风格 设计的软件可以更简洁,更有层次,更容易实现缓存等机制

### 2.功能

- 资源：互联网所有的事物都可以被抽象为资源
- 资源操作：使用POST、DELETE、PUT、GET，分别对应 添加、 删除、修改、查询。使用不同方法对资源进行操作。

### 3.传统方式操作资源

通过不同的参数来实现不同的效果！方法单一，post 和 get

- http://127.0.0.1/item/queryItem.action?id=1 查询,GET
- http://127.0.0.1/item/saveItem.action 新增,POST
- http://127.0.0.1/item/updateItem.action 更新,POST
- http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST

```java

@Controller
public class RestFulController {
    //因为传入了两个参数,且默认调用的get方法所以实际url应该是http://localhost:8080/t1?a=1&b=2
    @RequestMapping("/t1")
    public String test1(int a, int b, Model model) {
        model.addAttribute("msg", "test1的结果为" + (a + b));
        return "test";
    }
}
```

我们可以看到地址栏会把参数等显示,会把系统内部的变量暴露在url中,不太安全,并且url不好看

### 4.使用RestFul操作资源

可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！查询地址一样, 而执行方法不一样的简单实现,就是Controller类上定义一样的url地址,且不同表单

- http://127.0.0.1/item/1 查询,GET
- http://127.0.0.1/item 新增,POST
- http://127.0.0.1/item 更新,PUT
- http://127.0.0.1/item/1 删除,DELETE

实现也很简单,只需要在那些要从前端传入的参数前加上@PathVariable即可

```java

@Controller
public class RestFulController {

    //在对应属性前加上@PathVariable,代表让方法参数的值对应绑定到一个URI模板变量上。
    //这样就可以使用RestFul风格,只需要在@RequestMapping中{对应参数}就可以取值,
    // 这里实际的url是http://localhost:8080/t1/1/2,1是a的值,2是b的值
    @RequestMapping(value = "/t1/{a}/{b}")
    public String test1(@PathVariable int a, @PathVariable int b, Model model) {
        model.addAttribute("msg", "test1的结果为" + (a + b));
        return "test";
    }
}
```

#### 好处:

- 使路径变得更加简洁；
- 获得参数更加方便，框架会自动进行类型转换。
- 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败。

### 5.使用method属性指定请求类型

可以在@RequestMapping中加上参数method, 用于约束请求的类型，可以收窄请求范围。 指定请求谓词的类型如GET, POST, HEAD, OPTIONS, PUT, PATCH, DELETE, TRACE等

- 如果是只有一个默认参数,也就是只有访问地址(在注解里是name()),那么参数名可以缺省
- 但如果有两个及以上的参数,就不能缺省了,且不能用name="/hello",因为注解里给他起了别名(path或value), 所以完整的应该是@RequestMapping(value = "/test",method =
  RequestMethod.GET)

```java

@Controller
public class RestFulController {
    //所有的地址栏请求默认都会是 HTTP GET 类型的,所以如果设置方法为POST,会报405
    @RequestMapping(value = "/t1/{a}/{b}", method = RequestMethod.GET)
    public String test1(@PathVariable int a, @PathVariable int b, Model model) {
        model.addAttribute("msg", "test1的结果为" + (a + b));
        return "test";
    }
}
```

### 6.更简单的方式(也是正常开发中使用的)

方法级别的注解变体有如下几个：组合注解

- GetMapping("/hello")    -->Get请求方法
- PostMapping("/hello")   -->Post请求方法
- PutMapping("/hello")    -->Put请求方法
- DeleteMapping("/hello") -->Delete请求方法
- PatchMapping("/hello")  -->Patch请求方法

```java
@GetMapping("/t2/{a}/{b}")
public String test2(@PathVariable int a,@PathVariable int b,Model model){
        model.addAttribute("msg","test1的结果为"+(a+b));
        return"test";
        }
```

## 结果跳转方式

### 1.ModelAndView

- 一般实现Controller接口,重写它的一个方法,那个方法的返回值为ModelAndView(基本不会用这个方式)
###2.通过ServletAPI
- 自己定义HttpServletRequest req, HttpServletResponse rsp,来实现结果跳转
- 不需要视图解析器,因为你就算引入也不起作用,
```java
@Controller
@RequestMapping("/result")
public class ResultGo {
    //访问http://localhost:8080/result/t1来进入http://localhost:8080/tset.jsp
    //重定向的跳转地址是不能设置本来就不能直接访问到的地址的,比如WEB-INF目录下的文件
    @GetMapping("/t1")
    public void test1(HttpServletRequest req, HttpServletResponse resp, Model model) throws IOException {
        resp.sendRedirect("/tset.jsp");
    }
  //访问http://localhost:8080/result/t2,来进入http://localhost:8080/WEB-INF/jsp/test.jsp
    //请求转发,可以访问原本不能直接访问到的地址,比如WEB-INF目录下的文件
    @GetMapping("/t2")
    public void test2(HttpServletRequest req, HttpServletResponse resp, Model model) throws IOException, ServletException {
        model.addAttribute("msg","hanser");
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
    }
}
```
### 通过SpringMVC

通过SpringMVC来实现转发和重定向,我们知道,在类上加上@Controller后,这个类交给容器托管,里面的返回值为String的方法的返回值都被视为一个逻辑视图

- 如果没有使用视图解析器,那么返回值就要写全,地址要求跟上面API实现一样,
```java
@Controller
@RequestMapping("/result")
public class ResultGo {
    @GetMapping("/t3")
    public String test3(Model model) {
        model.addAttribute("msg","默认方式");
        //默认时使用请求转发实现的,观察地址栏不变就可以得出
        return "/tset.jsp";
    }

    @GetMapping("/t4")
    public String test4(Model model) {
        model.addAttribute("msg","请求转发");
        //请求转发使用forward:来限定方式，:后面要写完整的地址，因为加上forward:是不走视图解析器的
        return "forward:/tset.jsp";
    }

    @RequestMapping("/t5")
    public String test5(Model model) {
        model.addAttribute("msg","重定向");
        //重定向,会出现参数传递不了的情况，而且redirect:和上面一样，也不走视图解析器，而且本来重定向也不走视图解析器
        return "redirect:/tset.jsp";
    }
}
```
- 使用了视图解析器
```java
@Controller
@RequestMapping("/result")
public class ResultGo {
  @RequestMapping("/t6")
  public String test6(Model model) {
    model.addAttribute("msg", "请求转发");
    //默认的就是请求转发
    return "test";
  }

  @RequestMapping("/t7")
  public String test7(Model model) {
    model.addAttribute("msg", "重定向");
    //要想采取重定向的方式,就这样
    //重定向 , 不需要视图解析器 , 本质就是重新请求一个新地方嘛 , 所以注意路径问题.参数传不过去
    return "redirect:/tset.jsp";
  }
}
```

## 数据处理

### 处理提交数据

- 提交的域名称和处理方法的参数名一致
```java
//访问的url为http://localhost:8080/hello?name=hanser
@RequestMapping("/hello")
public String hello(String name){
   System.out.println(name);
   return "hello";
}
//下面时使用RestFul风格,亲测提交的表单域和对象的属性名要一致,因为好像@RequestParam不起作用
@RequestMapping("/t1/{name}")
public String test2(Model model, @PathVariable String name){
   System.out.println(name);
   return "test";
}
```
那么控制台输出hanser
- 提交的域名称和处理方法的参数名不一致:使用@RequestParam注解
```java
//我们请求的是http://localhost:8080/t1?username=hanser
@RequestMapping("/t1")
public String test1(Model model,@RequestParam("username") String name){
    System.out.println(name);
    return "test";
}
```
那么控制台输出hanser,我们建议在每一个从前端获取的参数前加上@RequestParam,来表示这个参数是从前端获取的
- 提交的是一个对象
```java
//实体类
public class User {
   private int id;
   private String name;
   private int age;
   //构造
   //get/set
   //tostring()
}
```
要求提交的表单域和对象的属性名一致,参数使用对象即可
```java
//提交数据 : http://localhost:8080/user?name=hanser&id=1&age=15
@RequestMapping("/user")
public String user(User user){
   System.out.println(user);
   return "hello";
}

//对象要使用RestFul风格的话,传入参数经测试,目前只能一个属性一个属性传入
@RequestMapping("/t1/{id}/{name}/{age}")
public String test1(Model model, @PathVariable int id,@PathVariable String name,@PathVariable int age){
   User user = new User(id, name, age);
   System.out.println(user);
   return "test";
}
```
说明：如果使用对象的话，前端传递的参数名和对象名必须一致，否则就是null。

## 乱码问题解决

### 测试步骤

- 1、我们可以在首页(index.xml)编写一个提交的表单
```xml
<form action="/encode/t1" method="post">
    <input type="text" name="name">
    <br>
    <input type="submit">
</form>
```
- 2.后台编写对应的处理类Encoding
```java
@Controller
public class Encoding {
    @PostMapping("/encode/t1")
    public String test1(@RequestParam("name") String name, Model model){
        System.out.println(name);
        model.addAttribute("msg",name);
        return "test";
    }
}
```
- 3.我们发现输入中文会出现乱码:并且我们在网页检查发现,name在响应时就已经是乱码状态;
并且,我们在类中使用sout方法,发现name在从前端取过来时就已经乱码了,所以是jsp传入后端时出现了问题
###解决方法
- 可以像之前写servlet一样,写一个过滤器,用来处理指定的类,来解决
注意写完过滤器要在web.xml中配置,且要过滤所有文件,写/*而不是/
- 也可以使用SpringMVC提供的过滤器,也是在web.xml中配置
```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```
改了配置文件,要重启服务器
- 但是我们发现 , 有些极端情况下.这个过滤器对get的支持不好,可以去网上找大神写的自定义过滤器

### 发现

springMVC已经自动解决了get方法中的乱码问题,post方法的乱码问题也可以写死在web.xml文件中

