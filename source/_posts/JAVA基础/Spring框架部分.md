---
title: Spring部分
categories: 计算机基础
tags: 
- 面试
- Spring
---

本文参考[马士兵java面试八股文](https://blog.csdn.net/weixin_57034203/article/details/125471520)，进行了梳理和分类，作为面试笔记留档

## 你觉得Spring的核心是什么？

spring是为了简化企业开发而生的，使得开发变得更加优雅和简洁。

 spring是一个**IOC**和**AOP**的容器框架。

 IOC：控制反转

 AOP：面向切面编程

 容器：包含并管理应用对象的生命周期，就好比用桶装水一样，spring就是桶，而对象就是水

## 如何实现一个IOC容器

IOC(Inversion of Control),意思是控制反转，不是什么技术，而是一种设计思想，IOC意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。

 在传统的程序设计中，我们直接在对象内部通过new进行对象创建，是程序主动去创建依赖对象，而IOC是有专门的容器来进行对象的创建，我们需要做的只是把对象放入容器中，需要时从容器中取即可，即IOC容器来控制对象的创建。

1. 先准备一个基本的容器对象，包含一些map结构的集合，用来方便后续过程中存储具体的对象
2. 进行配置文件的读取工作或者注解的解析工作，将需要创建的bean对象都封装成BeanDefinition对象存储在容器中
3. 容器将封装好的BeanDefinition对象通过反射的方式进行实例化，完成对象的实例化工作
4. 进行对象的初始化操作，也就是给类中的对应属性值就行设置，也就是进行依赖注入，完成整个对象的创建，变成一个完整的bean对象，存储在容器的某个map结构中
5. 通过容器对象来获取对象，进行对象的获取和逻辑处理工作
6. 提供销毁操作，当对象不用或者容器关闭的时候，将无用的对象进行销毁

## 说说你对Spring 的理解

Spring 使创建 Java 企业应用程序变得更加容易。它提供了在企业环境中接受 Java 语言所需的一切,，并支持 Groovy 和 Kotlin 作为 JVM 上的替代语言，并可根据应用程序的需要灵活地创建多种体系结构。 从 Spring Framework 5.0 开始，Spring 需要 JDK 8(Java SE 8+)，并且已经为 JDK 9 提供了现成的支持。

Spring支持各种应用场景， 在大型企业中, 应用程序通常需要运行很长时间，而且必须运行在 jdk 和应用服务器上，这种场景开发人员无法控制其升级周期。 其他可能作为一个单独的jar嵌入到服务器去运行，也有可能在云环境中。还有一些可能是不需要服务器的独立应用程序(如批处理或集成的工作任务)。

Spring 是开源的。它拥有一个庞大而且活跃的社区，提供不同范围的，真实用户的持续反馈。这也帮助Spring不断地改进,不断发展。

## 你觉得Spring的核心是什么？

 spring是一个开源框架。是为了简化企业开发而生的，使得开发变得更加优雅和简洁。

 spring是一个控制反转(IOC)和面向切面编程(AOP)的轻量级开源容器框架。

> 容器：包含并管理应用对象的生命周期，就好比用桶装水一样，spring就是桶，而对象就是水

## 说一下使用spring的优势？

1. Spring通过DI、AOP和消除样板式代码(xxxTemplate)来简化企业级Java开发
2. Spring框架之外还存在一个构建在核心框架之上的庞大生态圈，它将Spring扩展到不同的领域，如Web服务、REST、移动开发以及NoSQL
3. 低侵入式设计，代码的污染极低
4. 独立于各种应用服务器，基于Spring框架的应用，可以真正实现Write Once,Run Anywhere的承诺
5. Spring的IOC容器降低了业务对象替换的复杂性，提高了组件之间的解耦
6. Spring的AOP支持允许将一些通用任务如安全、事务、日志等进行集中式处理，从而提供了更好的复用
7. Spring的ORM和DAO提供了与第三方持久层框架的的良好整合，并简化了底层的数据库访问
8. Spring的高度开放性，并不强制应用完全依赖于Spring，开发者可自由选用Spring框架的部分或全部

## Spring是如何简化开发的？

- 基于POJO的轻量级和最小侵入性编程
- 通过依赖注入和面向接口(Spring提供了大量可拓展的接口，可以根据业务逻辑进行拓展)实现松耦合
-  基于切面和惯例进行声明式编程
- 通过切面和模板减少样板式代码

## 说说你对Aop的理解？

面向切面编程。它是为解耦而生的，解耦是程序员编码开发过程中一直追求的境界，AOP在业务类的隔离上，绝对是做到了解耦，在这里面有几个核心的概念：

- 切面（Aspect）: 指关注点模块化，这个关注点可能会横切多个对象。事务管理是企业级Java应用中有关横切关注点的例子。 在Spring AOP中，切面可以使用通用类基于模式的方式（schema-based approach）或者在普通类中以@Aspect注解（@AspectJ 注解方式）来实现。
- 连接点（Join point）: 在程序执行过程中某个特定的点，例如某个方法调用的时间点或者处理异常的时间点。在Spring AOP中，一个连接点总是代表一个方法的执行。
- 通知（Advice）: 在切面的某个特定的连接点上执行的动作。通知有多种类型，包括“around”, “before” and “after”等等。通知的类型将在后面的章节进行讨论。 许多AOP框架，包括Spring在内，都是以拦截器做通知模型的，并维护着一个以连接点为中心的拦截器链。
- 切点（Pointcut）: 匹配连接点的断言。通知和切点表达式相关联，并在满足这个切点的连接点上运行（例如，当执行某个特定名称的方法时）。切点表达式如何和连接点匹配是AOP的核心：Spring默认使用AspectJ切点语义。
- 引入（Introduction）: 声明额外的方法或者某个类型的字段。Spring允许引入新的接口（以及一个对应的实现）到任何被通知的对象上。例如，可以使用引入来使bean实现 IsModified接口， 以便简化缓存机制（在AspectJ社区，引入也被称为内部类型声明（inter））。
- 目标对象（Target object）: 被一个或者多个切面所通知的对象。也被称作被通知（advised）对象。既然Spring AOP是通过运行时代理实现的，那么这个对象永远是一个被代理（proxied）的对象。
- AOP代理（AOP proxy）:AOP框架创建的对象，用来实现切面契约（aspect contract）（包括通知方法执行等功能）。在Spring中，AOP代理可以是JDK动态代理或CGLIB代理。
- 织入（Weaving）: 把切面连接到其它的应用程序类型或者对象上，并创建一个被被通知的对象的过程。这个过程可以在编译时（例如使用AspectJ编译器）、类加载时或运行时中完成。 Spring和其他纯Java AOP框架一样，是在运行时完成织入的。

任何一个系统都是由不同的组件组成的，每个组件负责一块特定的功能，当然会存在很多组件是跟业务无关的，例如日志、事务、权限等核心服务组件，这些核心服务组件经常融入到具体的业务逻辑中，如果我们为每一个具体业务逻辑操作都添加这样的代码，很明显代码冗余太多，因此我们需要将这些公共的代码逻辑抽象出来变成一个切面，然后注入到目标对象（具体业务）中去，AOP正是基于这样的一个思路实现的，通过动态代理的方式，将需要注入切面的对象进行代理，在进行调用的时候，将公共的逻辑直接添加进去，而不需要修改原有业务的逻辑代码，只需要在原来的业务逻辑基础之上做一些增强功能即可。

## 说说你对IOC的理解

控制反转。

先说控制，控制谁？在实现过程中所需要的对象以及需要依赖的对象；

然后是反转。反转是相对于过去的对象创建而言。传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，控制权在我们手里；反转则是由容器来帮忙创建及注入依赖对象，为何是反转？**因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象**。哪些方面反转了？**依赖对象的获取被反转了**。

## BeanFactory和ApplicationContext的区别

**相同点：**

- Spring提供了两种不同的IOC 容器，一个是BeanFactory，另外一个是ApplicationContext，它们都是Java interface，ApplicationContext继承于BeanFactory(ApplicationContext继承ListableBeanFactory，而ListableBeanFactory继承BeanFactory)
- 它们都可以用来配置XML属性，也支持属性的自动注入。
- BeanFactory 和 ApplicationContext 都提供了一种方式，使用getBean(“bean name”)获取bean。

**不同：**

- 当你调用getBean()方法时，BeanFactory仅实例化bean，而ApplicationContext 在启动容器的时候实例化单例bean，不会等待调用getBean()方法时再实例化。
- BeanFactory不支持国际化，即i18n，但ApplicationContext提供了对它的支持。
- BeanFactory 的一个核心实现是XMLBeanFactory，而ApplicationContext 的一个核心实现是ClassPathXmlApplicationContext，Web容器的环境我们使用WebApplicationContext并且增加了getServletContext 方法。
- 如果使用自动注入并使用BeanFactory，则需要使用API注册AutoWiredBeanPostProcessor，如果使用ApplicationContext，则可以使用XML进行配置。

简而言之，BeanFactory提供基本的IOC和DI功能，而ApplicationContext提供高级功能，BeanFactory可用于测试和非生产使用，但ApplicationContext是功能更丰富的容器实现，应该优于BeanFactory

## 简述Spring bean的生命周期

![image-20230311144026315](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303111440400.png)

1. 实例化bean对象。通过反射的方式进行对象的创建，此时的创建只是在堆空间中申请空间，属性都是默认值
2. 设置对象属性。 给对象中的属性进行值的设置工作
3. 检查Aware相关接口并设置相关依赖。如果对象中需要引用容器内部的对象，那么需要调用aware接口的子类方法来进行统一的设置
4. BeanPostProcessor的前置处理。对生成的bean对象进行前置的处理工作
5. 检查是否是InitializingBean的子类来决定是否调用afterPropertiesSet方法。判断当前bean对象是否设置了InitializingBean接口，然后进行属性的设置等基本工作。
6. 检查是否配置有自定义的init-method方法。如果当前bean对象定义了初始化方法，那么在此处调用初始化方法
7. BeanPostProcessor后置处理。对生成的bean对象进行后置的处理工作
8. 注册必要的Destruction相关回调接口。为了方便对象的销毁，在此处调用注销的回调接口，方便对象进行销毁操作
9. 获取并使用bean对象。通过容器来获取对象并进行使用
10. 是否实现DisposableBean接口。判断是否实现了DisposableBean接口，并调用具体的方法来进行对象的销毁工作
11. 是否配置有自定义的destory方法。如果当前bean对象定义了销毁方法，那么在此处调用销毁方法

## spring支持的bean作用域

- singleton：单例模式(spring中bena的默认作用域)。使用该属性定义Bean时，IOC容器仅创建一个Bean实例，IOC容器每次返回的是同一个Bean实例。
- prototype。使用该属性定义Bean时，IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例。
- request。该属性仅对HTTP请求产生作用，使用该属性定义Bean时，每次HTTP请求都会创建一个新的Bean，适用于WebApplicationContext环境。
- session。该属性仅用于HTTP Session，同一个Session共享一个Bean实例。不同Session使用不同的实例。
- global-session。该属性仅用于HTTP Session，同session作用域不同的是，所有的Session共享一个Bean实例。

## Spring框架中的单例Bean是线程安全的么？

Spring中的Bean对象默认是单例的，框架并没有对bean进行多线程的封装处理。

如果Bean是有状态的，那么就需要开发人员自己来保证线程安全的保证，最简单的办法就是改变bean的作用域把singleton改成prototype，这样每次请求bean对象就相当于是创建新的对象来保证线程的安全

有状态就是有数据存储的功能；无状态就是不会存储数据，你想一下，我们的controller，service和dao本身并不是线程安全的，只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制遍历，这是自己线程的工作内存，是最安全的。

因此在进行使用的时候，不要在bean中声明任何有状态的实例变量或者类变量，如果必须如此，也推荐大家使用ThreadLocal把变量变成线程私有，如果bean的实例变量或者类变量需要在多个线程之间共享，那么就只能使用synchronized，lock，cas等这些实现线程同步的方法了。

## spring框架中使用了哪些设计模式及应用场景

1. 工厂模式，在各种BeanFactory以及ApplicationContext创建中都用到了
2. 模版模式，在各种BeanFactory以及ApplicationContext实现中也都用到了
3. 代理模式，Spring AOP 利用了 AspectJ AOP实现的! AspectJ AOP 的底层用了动态代理
4. 策略模式，加载资源文件的方式，使用了不同的方法，比如：ClassPathResourece，FileSystemResource，ServletContextResource，UrlResource但他们都有共同的借口Resource；在Aop的实现中，采用了两种不同的方式，JDK动态代理和CGLIB代理
5. 单例模式，比如在创建bean的时候，默认为单例模式
6. 观察者模式，spring中的ApplicationEvent，ApplicationListener,ApplicationEventPublisher
7. 适配器模式，MethodBeforeAdviceAdapter,ThrowsAdviceAdapter,AfterReturningAdapter
8. 装饰者模式，源码中类型带Wrapper或者Decorator的都是

## spring事务的实现方式原理

在使用Spring框架的时候，可以有两种事务的实现方式，一种是编程式事务，有用户自己通过代码来控制事务的处理逻辑，还有一种是声明式事务，通过@Transactional注解来实现。

 其实事务的操作本来应该是由数据库来进行控制，但是为了方便用户进行业务逻辑的操作，spring对事务功能进行了扩展实现，一般我们很少会用编程式事务，更多的是通过添加@Transactional注解来进行实现，当添加此注解之后事务的自动功能就会关闭，有spring框架来帮助进行控制。

 其实事务操作是AOP的一个核心体现，当一个方法添加@Transactional注解之后，spring会基于这个类生成一个代理对象，会将这个代理对象作为bean，当使用这个代理对象的方法的时候，如果有事务处理，那么会先把事务的自动提交给关闭，然后去执行具体的业务逻辑，如果执行逻辑没有出现异常，那么代理逻辑就会直接提交，如果出现任何异常情况，那么直接进行回滚操作，当然用户可以控制对哪些异常进行回滚操作。

## spring事务的隔离级别

 spring中的事务隔离级别就是数据库的隔离级别，有以下几种：

- read uncommitted。读未提交
- read committed。读已提交
- repeatable read 可重复读
- 串行化

 在进行配置的时候，如果数据库和spring代码中的隔离级别不同，那么以spring的配置为主。

## spring的事务传播机制

[带你读懂Spring 事务——事务的传播机制](https://zhuanlan.zhihu.com/p/148504094)

## spring事务什么时候会失效？

1. bean对象没有被spring容器管理
2. 方法的访问修饰符不是public
3. 自身调用问题
4. 数据源没有配置事务管理器
5. 数据库不支持事务
6. 异常被捕获
7. 异常类型错误或者配置错误

## 什么的是bean的自动装配，它有哪些方式？

bean的自动装配指的是bean的属性值在进行注入的时候通过某种特定的规则和方式去容器中查找，并设置到具体的对象属性中，主要有五种方式：

1. no – 缺省情况下，自动配置是通过“ref”属性手动设定，在项目中最常用
2.  byName – 根据属性名称自动装配。如果一个bean的名称和其他bean属性的名称是一样的，将会自装配它。
3. byType – 按数据类型自动装配，如果bean的数据类型是用其它bean属性的数据类型，兼容并自动装配它。
4.  constructor – 在构造函数参数的byType方式。
5. autodetect – 如果找到默认的构造函数，使用“自动装配用构造”; 否则，使用“按类型自动装配”。

## spring、springmvc、springboot的区别

1. spring是一个一站式的轻量级的java开发框架，核心是控制反转（IOC）和面向切面（AOP），针对于开发的WEB层(springMVC)、业务层(IOC)、持久层(jdbcTemplate)等都提供了多种配置解决方案；
2. springMVC是spring基础之上的一个MVC框架，主要处理web开发的路径映射和视图渲染，属于spring框架中WEB层开发的一部分；
3. springBoot框架相对于springMVC框架来说，更专注于开发微服务后台接口，不开发前端视图，同时遵循约定大于配置原则，简化了插件配置流程，不需要配置xml，相对springmvc，大大简化了配置流程；

总结：

- Spring 框架就像一个家族，有众多衍生产品例如 boot、security、jpa等等。但他们的基础都是Spring的ioc、aop等. ioc 提供了依赖注入的容器， aop解决了面向横切面编程，然后在此两者的基础上实现了其他延伸产品的高级功能；
- springMVC主要解决WEB开发的问题，是基于Servlet 的一个MVC框架，通过XML配置，统一开发前端视图和后端逻辑；
- 由于Spring的配置非常复杂，各种XML、JavaConfig、servlet处理起来比较繁琐，为了简化开发者的使用，从而创造性地推出了springBoot框架，约定大于配置，简化了springMVC的配置流程；但区别于springMvc的是，springBoot专注于单体微服务接口开发，和前端解耦，虽然springBoot也可以做成springMVC前后台一起开发，但是这就有点不符合springBoot框架的初衷了；

## springmvc的九大组件

### HandlerMapping

根据request找到相应的处理器。因为Handler（Controller）有两种形式，一种是基于类的Handler，另一种是基于Method的Handler（也就是我们常用的）

### HandlerAdapter

调用Handler的适配器。如果把Handler（Controller）当做工具的话，那么HandlerAdapter就相当于干活的工人

### HandlerExceptionResolver

对异常的处理

### ViewResolver

用来将String类型的视图名和Locale解析为View类型的视图

### RequestToViewNameTranslator

有的Handler（Controller）处理完后没有设置返回类型，比如是void方法，这是就需要从request中获取viewName

### LocaleResolver

从request中解析出Locale。Locale表示一个区域，比如zh-cn，对不同的区域的用户，显示不同的结果，这就是i18n（SpringMVC中有具体的拦截器LocaleChangeInterceptor）

### ThemeResolver

主题解析，这种类似于我们手机更换主题，不同的UI，css等

### MultipartResolver

处理上传请求，将普通的request封装成MultipartHttpServletRequest

### FlashMapManager

用于管理FlashMap，FlashMap用于在redirect重定向中传递参数

## springMVC工作流程

![image-20230307212944755](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303072129864.png)

SpringMVC底层工作原理：和上面图一一对应,实线表示已经被MVC实现了,虚线表示我们自己要写:

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

## 如何理解springboot中的starter

使用spring+springmvc框架进行开发的时候，如果需要引入mybatis框架，那么需要在xml中定义需要的bean对象，这个过程很明显是很麻烦的，如果需要引入额外的其他组件，那么也需要进行复杂的配置，因此在springboot中引入了starter

starter就是一个jar包，写一个@Configuration的配置类，将这些bean定义在其中，然后再starter包的META-INF/spring.factories中写入配置类，那么springboot程序在启动的时候就会按照约定来加载该配置类

开发人员只需要将相应的starter包依赖进应用中，进行相关的属性配置，就可以进行代码开发，而不需要单独进行bean对象的配置

## 什么是嵌入式服务器，为什么使用嵌入式服务器？

在springboot框架中，大家应该发现了有一个内嵌的tomcat，在之前的开发流程中，每次写好代码之后必须要将项目部署到一个额外的web服务器中，只有这样才可以运行，这个明显要麻烦很多，而使用springboot的时候，你会发现在启动项目的时候可以直接按照java应用程序的方式来启动项目，不需要额外的环境支持，也不需要tomcat服务器，这是因为在springboot框架中内置了tomcat.jar，来通过main方法启动容器，达到一键开发部署的方式，不需要额外的任何其他操作。
