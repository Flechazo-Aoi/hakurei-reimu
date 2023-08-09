---
title: spring(九)：aop
categories: java
tags:
- spring
- 框架
---

## 什么是aop

- 是通过预编译的方式和运行期动态代理实现程序功能的统一维护的一种技术。

## AOP在Spring中的作用

**提供声明式事务：允许用户自定义切面**

- 横切关注点：跨越应用程序多个模块的方法或功能。即：与我们逻辑无关的，但是我们需要专注的部分，就是横切关注点。如：日志，安全，缓存，事务等等
- 切面（aspect）：横切关注点被 模块化 的特殊对象。即：它是一个类
- 通知（advice）：切面必须要完成的工作。即：它是类中的一个方法
- 目标（target）：被通知的对象
- 代理（proxy）：向目标对象应用通知之后创建的对象
- 切入点（PointCut）：切面通知 执行的 “地点” 的定义
- 连接点（joinPoint）：与切入点匹配的执行点

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice：
![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141016744.png)
即AOP在不改变原有代码的情况下，去增加新的功能。

## 使用spring实现AOP

使用AOP织入，需要导入一个依赖包

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

之后要在xml配置文件中添加约束

```
xmlns:aop="http://www.springframework.org/schema/aop"
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd
```

### 方式1:使用spring的API接口

动态代理代理的是接口，因为aop用到的是动态代理，所以之后使用getBean方法获取 bean的实例的时候，映射类型需要为接口类型，而不能是实现类类型!，静态代理代理的是实体类

- 首先编写类来继承Advice接口

```java
//程序返回之后加入日志
public class AfterLog implements AfterReturningAdvice {
    //method:   要执行的目标对象的方法
    //args:     参数
    //target:   目标对象
    //比MethodBeforeAdvice多了一个参数returnValue，代表目标对象返回后的返回值
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, @Nullable Object target) throws Throwable {
        System.out.println("执行了" + target.getClass().getName() + "的" + method.getName() + "方法，返回结果为:" + returnValue);
    }
}
```

- 然后去spring的文件中注册 , 并实现aop切入实现 , 注意导入约束，配置applicationContext.xml文件

```xml
<aop:config>
    <!--切入点(pointcut):id(切入点的名字);expression(表达式,代表切入的位置)
       ="execution【要执行的位置!】 (*(修饰词) *(包名) *(类名) *(方法名) *(参数))"
       * com.hanser.service.UserServiceImpl.*(..)就表示所有类型的，com.hanser.service包下的UserServiceImpl类的所有方法，参数任意-->
    <aop:pointcut id="pointcut" expression="execution(* com.hanser.service.UserServiceImpl.*(..))"/>
    <!--执行环绕添加,advice-ref:被切入的类，引用前要先在spring中注册,pointcut-ref切入点,就是上面定义的切入点-->
    <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
</aop:config>
```

- 测试

```java
public class MyTest {

    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //AOP的底层是由动态代理实现的，动态代理代理的是接口，所以在使用getBean方法获取bean的实例的时候，映射类型需要为接口类型，而不能是实现类类型!
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
    }
}

```

### 方式2:使用自定义类(这个类就是上面的切面)实现AOP

- 自己定义一个包含一些方法(对应上面的通知)的一个类(对应上面的切面)
```java
//自定义类来实现AOP
public class Diy {
    public void before(){
        System.out.println("=========方法执行前=======");
    }
    public void after(){
        System.out.println("=========方法执行后=======");
    }
}
```
- 在xml中使用切面的方式进行配置

```xml
<!--方式2 使用自定义类来实现AOP-->
<aop:config>
    <!--定义一个切面(也就是包含一系列通知(类中方法)的一个类)
    id就是名字,ref就是这个类的引用,引用前也要先注册-->
    <aop:aspect id="diy" ref="diy">
        <!--切面中定义一些方法，包括切入点(pointcut)，切入方法等-->
        <aop:pointcut id="point" expression="execution(* com.hanser.service.UserServiceImpl.*(..))"/>
        <!--aop:before代表在方法前切入,method代表diy类的方法名,pointcut-ref是切入点-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:after method="after" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```

这样做，能做的事情有限，但是方便，写起来简单

### 方式3:使用注解实现AOP
需要引入注解支持:<aop:aspectj-autoproxy/>，然后编写一个类在对应的类或方法上加上注解即可。其实和方法2类似，只是不用在xml中配置，而是直接在类上写注解

**用到的注解：**

- @Aspect()：声明在类上面，代表这是一个切面
- @Before()：代表是在代理对象的方法执行前切入,()中要传入参数，
"execution(* com.hanser.service.UserServiceImpl.*(..))"代表切入哪里
- @After()：代表是在代理对象的方法执行后切入,()中要传入参数同@Before()
- @around()：代表是在代理对象的方法执行环绕切入,()中要传入参数同@Before()
```java
//方式3：使用注解实现AOP
//@Aspect标注这个类是一个切面
@Aspect
@Component
public class AnnotationPointCut {
    @Before("execution(* com.hanser.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("芜湖");
    }
    @After("execution(* com.hanser.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("hanser");
    }
    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.hanser.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint pj) throws Throwable {
        //这行会在@Before切入之前切入
        System.out.println("========环绕前=========");
        //代表执行方法
        Object proceed = pj.proceed();
        //这行会在@After切入之前切入
        System.out.println("========环绕后=========");
    }
}
```