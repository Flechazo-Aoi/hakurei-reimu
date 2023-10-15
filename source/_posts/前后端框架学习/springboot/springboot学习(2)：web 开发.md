---
title: springboot学习(二)：web 开发
categories: 前后端框架学习
date: 2022-12-21  15:22:33
tags: 
- springboot
- 框架
---



 要解决的问题：

- 导入静态资源
- 首页
- jsp，模板引擎Thymeleaf 
- 装配拓展SpringMVC
- 增删改查
- 拦截器
- 国际化

# 静态资源的导入

按照之前的逻辑，找到mvc的自动配置类WebMvcAutoConfiguration.java，发现有一个方法

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    // 说明如果自定义了配置，那么默认的配置失效，直接返回
	if (!this.resourceProperties.isAddMappings()) {
		logger.debug("Default resource handling disabled");
		return;
	}
	addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
	addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
		registration.addResourceLocations(this.resourceProperties.getStaticLocations());
		if (this.servletContext != null) {
			ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLET_LOCATION);
			registration.addResourceLocations(resource);
		}
	});
}
```

### 第一种：webjars

WebJars是将web前端资源（js，css等）打成jar包文件，然后借助Maven工具，以jar包形式对web前端资源进行统一依赖管理，保证这些Web资源版本唯一性。WebJars的jar包部署在Maven中央仓库上。

也就是用maven引入jar包，并且用这种方式引入的jar包会自动到`classpath:/META-INF/resources/webjars/`下，而我们访问静态资源时，输入的路径为localhost:8080/webjars/**，这个路径会自动映射到classpath:/META-INF/resources/webjars/，

但是一般不会使用这种方式

### 第二种：

点进getStaticPathPattern()

```java
public String[] getStaticLocations() {
	return this.staticLocations;
}
```

再找到this.staticLocations

```java
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
		"classpath:/resources/", "classpath:/static/", "classpath:/public/" }
/**
 * Locations of static resources. Defaults to classpath:[/META-INF/resources/
 * /resources/, /static/, /public/].
 */
private String[] staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
```

所以我们的静态资源可以放在

- classpath:/META-INF/resources/
- classpath:/resources/
- classpath:/static/
- classpath:/public/

第一种和webjars类似，一般不用

后面三种访问时的路径为`localhost:8080/**`，这个路径会自动映射到三个路径中，且优先级为：resources>static（默认）>puiblic，

# 定制首页

依旧是再WevMvcAutoConfiguration里面找，发现有一个

![image-20221126103344226](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261045877.png)

上半部分是遍历静态资源的路径，并调用getIndexHtml方法

![image-20221126104228078](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261045890.png)

找到一个叫index.html的页面，如果存在，则能正常得到首页，否则去servletContext里找，还是找不到的话，就返回null

所以我们得到结论：首页可以放在上面静态资源放的位置，且名称为index.html

# thymleaf模板引擎

## 模板引擎

前端交给我们的页面，是html页面。如果是我们以前开发，我们需要把他们转成jsp页面，jsp好处就是当我们查出一些数据转发到JSP页面以后，我们可以用jsp轻松实现数据的显示及交互等。

jsp支持非常强大的功能，包括能写Java代码，但是呢，我们现在的这种情况，SpringBoot这个项目首先是以jar的方式，不是war，像第二，我们用的还是嵌入式的Tomcat，所以呢，**他现在默认是不支持jsp的**。

那不支持jsp，如果我们直接用纯静态页面的方式，那给我们开发会带来非常大的麻烦，那怎么办呢？

**SpringBoot推荐你可以来使用模板引擎：**

模板引擎，我们其实大家听到很多，其实jsp就是一个模板引擎，还有用的比较多的freemarker，包括SpringBoot给我们推荐的Thymeleaf，模板引擎有非常多，但再多的模板引擎，他们的思想都是一样的，什么样一个思想呢我们来看一下这张图：

![image-20221126160222898](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261602953.png)

模板引擎的作用就是我们来写一个页面模板，比如有些值呢，是动态的，我们写一些表达式。而这些值，从哪来呢，就是我们在后台封装一些数据。然后把这个模板和这个数据交给我们模板引擎，模板引擎按照我们这个数据帮你把这表达式解析、填充到我们指定的位置，然后把这个数据最终生成一个我们想要的内容给我们写出去，这就是我们这个模板引擎，不管是jsp还是其他模板引擎，都是这个思想。只不过呢，就是说不同模板引擎之间，他们可能这个语法有点不一样。其他的我就不介绍了，我主要来介绍一下SpringBoot给我们推荐的Thymeleaf模板引擎，这模板引擎呢，是一个高级语言的模板引擎，他的这个语法更简单。而且呢，功能更强大。

## 引入Thymeleaf

```xml
<!--引入thymeleaf-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

且在ThymeleafProperties.java文件中我们可以得知，默认的路径是classpath:/templates/，templates这个目录跟之前的WEB-INF一样，是不能直接访问，只能通过视图跳转访问

![image-20221126160656620](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261606666.png)

**我们只需要把我们的html页面放在类路径下的templates下，thymeleaf就可以帮我们自动渲染了。**

**使用thymeleaf不需要什么配置，只需要将他放在指定的文件夹下即可！**

## Thymeleaf 语法

[Thymeleaf 官网](https://www.thymeleaf.org/)

[Thymeleaf 在Github 的主页](https://github.com/thymeleaf/thymeleaf)

- 简单表达式
  - 变量表达式：`${...}`
  - 选择变量表达式：`*{...}`
  - 消息表达式：`#{...}`
  - 链接网址表达式：`@{...}`
  - 片段表达式：`~{...}`

- 文字
  - 文本文本：，,…`'one text'``'Another one!'`
  - 数字文字： ， ， ， ,…`0``34``3.0``12.3`
  - 布尔文字： ，`true``false`
  - 空文本：`null`
  - 文字标记： ， ， ,…`one``sometext``main`
- 文本操作：
  - 字符串串联：`+`
  - 文字替换：`|The name is ${name}|`
- 算术运算：
  - 二元运算符： ， ， ， ， ，`+``-``*``/``%`
  - 减号（一元运算符）：`-`
- 布尔运算：
  - 二元运算符： ，`and``or`
  - 布尔否定（一元运算符）：，`!``not`
- 比较和平等：
  - 比较器： ， ， ， （ ， ， ，`>``<``>=``<=``gt``lt``ge``le`)
  - 等运算符： ， （，`==``!=``eq``ne`)
- 特殊代币：
  - 无操作：`_`

- Simple expressions:
  - Variable Expressions: `${...}`
  - Selection Variable Expressions: `*{...}`
  - Message Expressions: `#{...}`
  - Link URL Expressions: `@{...}`
  - Fragment Expressions: `~{...}`

- Literals
  - Text literals: , ,…`'one text'``'Another one!'`
  - Number literals: , , , ,…`0``34``3.0``12.3`
  - Boolean literals: , `true``false`
  - Null literal: `null`
  - Literal tokens: , , ,…`one``sometext``main`

- Text operations:
  - String concatenation: `+`
  - Literal substitutions: `|The name is ${name}|`
- Arithmetic operations:
  - Binary operators: , , , , `+``-``*``/``%`
  - Minus sign (unary operator): `-`
- Boolean operations:
  - Binary operators: , `and``or`
  - Boolean negation (unary operator): , `!``not`
- Comparisons and equality:
  - Comparators: , , , (, , , `>``<``>=``<=``gt``lt``ge``le`)
  - Equality operators: , (, `==``!=``eq``ne`)
- Conditional operators:
  - If-then: `(if) ? (then)`
  - If-then-else: `(if) ? (then) : (else)`
  - Default: `(value) ?: (defaultvalue)`
- Special tokens:
  - No-Operation: `_`

## 使用thymeleaf

```html
<!DOCTYPE html>
<html lang="en"  xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--所有的html元素都可以被thymelaeaf替换接管；th：元素名-->
<div th:text="${msg}"></div>
</body>
</html>
```

```java
package com.example.springboot03web.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

//在templates目录下的所有页面，只能通过controller跳转
//需要模板引擎的依赖
@Controller

public class IndexContriller {
    @RequestMapping("/test")
    public String test(Model model){
        model.addAttribute("msg","hello");
        return "test";
    }
}
```

![image-20220106113855870](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261620242.png)

# MVC配置原理

在进行项目编写前，我们还需要知道一个东西，就是SpringBoot对我们的SpringMVC还做了哪些配置，包括如何扩展，如何定制

[官方文档](https://docs.spring.io/spring-boot/docs/2.7.5.RELEASE/reference/htmlsingle/)

![image-20221126194127905](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211261941001.png)

意思就是如果我们要在默认的SpringMVC自动配置的基础上，进行拓展，只需要自己写一个类，加上`@Configuration`注解，而且我们注意到`WebMvcConfigurer`是一个接口，所以我们写的配置类去实现这个接口即可；另外，官方贴别标注了**不能加**`@EnableWebMvc`,这个我们读了源码就知道原因了，我们先试着重写接口的一个方法

## 重写 addViewControllers视图控制器方法

```java
@Configuration
public class MVCConfig implements WebMvcConfigurer {
    // 视图跳转控制
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 访问"localhost:8080/hanser"这个界面时，因为我们引入了模板引擎，所以最终路径为"classpath:/templates/test.html"
        registry.addViewController("/hanser").setViewName("test");
    }
}
```

然后启动项目，输入localhost:8080/hanser，发现正常跳转到了localhost:8080/test页面，说明我们的配置生效了

## 为什么不能加@EnableWebMvc

我们点进@EnableWebMvc源码查看

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({DelegatingWebMvcConfiguration.class})
public @interface EnableWebMvc {
}
```

发现他导入了`DelegatingWebMvcConfiguration`一个类

```java
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {}
```

而这个类实现了`WebMvcConfigurationSupport`，并且我们注意到`WebMvcAutoConfiguration`这个类有一个注解

```
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
```

说明只有当容器中不存在`WebMvcConfigurationSupport`这个类时，自动配置类才会生效，而我们使用了`@EnableWebMvc`注解后，自动导入了这个类，说明我们想全权接管springMVC的配置，所以会出现问题
