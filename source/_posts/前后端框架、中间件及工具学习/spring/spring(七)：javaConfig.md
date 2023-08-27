---
title: spring(七)：javaConfig
categories: 前后端框架、中间件及工具学习
tags:
- spring
- 框架
---

我们现在可以完全不使用Spring的xml配置,全权交给Java来做

javaConfig是Spring的一个子项目,在Spring4之后,他成为了一个核心功能

## 具体实现

- 编写一个配置类
```java
//@Configuration就是声明当前类是一个配置类,相当于之前的beans.xml,有了这个类就不用在去配置beans.xml
// 这个类也会Spring容器托管，注册到容器中，因为它本来就是一个@Component
@Configuration
public class MyConfig {
    // @Bean注册一个bean，就相当于我们之前写的一个bean标签
    // 这个方法的名字，就相当于bean标签中id属性
    // 这个方法的返回值，就相当于bean标签中的class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```
这样我们就能在测试类中得到这个配置文件中配置的得到对象的方法,与之前不同的是,使用的是AnnotationConfigApplicationContext来获取上下文对象
- 测试类
```java
public class MyTest {
    public static void main(String[] args) {
        //如果完全使用了配置类方式去做，我们就只能通过 AnnotationConfig 上下文来获取容器，通过配置类的class对象加载！
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        //这里getBean的第一个参数就是MyConfig的方法名,
        User user = context.getBean("getUser",User.class);
        System.out.println(user.getName());
    }
}
```
当然因为我们取到的对象没有给属性赋值,所以取出的值为空,要想实现原来`<bean>`标签中的`<property>`标签，只需在对应属性或set方法上加上@Value即可
