---
title: spring中的一些注解
categories: 
- 随笔
tags: 
  - java
  - spring
---

## @NotBlank、@NotEmpty和@NotNull

### @NotBlank

只能用在String字符串类型上，而且调用`trim()`后，长度必须大于0(size>0)

### @NotEmpty

主要用在集合类上面，加了@NotEmpty的String类、Collection、Map、数组，是不能为null而且长度必须大于0

### @NotNull

不能为null，但可以为empty

```java
// @NotNull: false
// @NotEmpty: false
// @NotBlank: false
String name = null;

// @NotNull: true
// @NotEmpty: false
// @NotBlank: false
String name = "";

// @NotNull: true
// @NotEmpty: true
// @NotBlank: false
String name = " ";
```

三个注解都放在属性上，用来对属性值进行一定的限制

```java
@NotBlank(message = "msg不能为空")
private String msg;
```

## @Link

写代码的时候，有时候，你需要写一些注释，把内容相互关联起来，方便自己或别人看的时候，可以直接找到你关联的代码类。这时`@Link`就排上用场了，点击对应类可以看到对应类的javaDoc注释

```java
/**
 * <p>自定义过滤器</p><br>
 *
 * 将原生{@link HttpServletRequestWrapper}对象替换成自定义的{@link RequestWrapper}对象
 *
 * @author  xxx
 * @date  2022年10月20日16:27:34
 **/
```

其效果如下图

![image-20230903203424589](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309032034679.png)

## @Value

在使用Spring框架的项目中，@Value是使用比较频繁的注解之一，它的作用是将配置文件中key对应的值赋值给它标注的属性。

### 支持形式

@Value属性注入功能根据注入的内容来源可分为两类：通过配置文件的属性注入和通过非配置文件的属性注入。

### 基于配置文件注入

Spring boot 项目中，可以使用`@Value(${})`取出默认路径下的配置文件中的值来给属性赋值

### 基于非配置文件注入

SpEL（Spring Expression Language）即Spring表达式语言，可以在运行时查询和操作数据。使用`#{...}`作为定界符, 所有在大括号中的字符都将被认为是 SpEL。

```java
/**
 * 注入普通字符串，相当于直接给属性默认值
 */
@Value("程序新视界")
private String wechatSubscription;

/**
 *  注入操作系统属性
 */
@Value("#{systemProperties['os.name']}")
private String systemPropertiesName;

/**
 * 注入表达式结果
 */
@Value("#{ T(java.lang.Math).random() * 100.0 }")
private double randomNumber;

/**
 * 注入其他Bean属性：注入config对象的属性tool
 */
@Value("#{config.tool}")
private String tool;

/**
 * 注入列表形式（自动根据"|"分割）
 */
@Value("#{'${words}'.split('\\|')}")
private List<String> numList;

/**
 * 注入文件资源
 */
@Value("classpath:config.xml")
private Resource resourceFile;

/**
 * 注入URL资源
 */
@Value("http://www.choupangxia.com")
private URL homePage;
```

### 默认值注入

无论使用`#{}`或`${}`进行属性的注入，当无法获取对应值时需要设置默认值，可以采用如下方式来进行设置。

```java
/**/**
 * 如果属性中未配置ip，则使用默认值
 */
@Value("${ip:127.0.0.1}")
private String ip;

/**
 * 如果系统属性中未获取到port的值，则使用8888。
 */
@Value("#{systemProperties['port']?:'8888'}")
private String port;
```

其中`${}`中直接使用`:`对未定义或为空的值进行默认值设置，而`#{}`则需要使用`?:`对未设置的属性进行默认值设置。

## @PostConstruct

如果我们需要在程序启动时就加载某些数据。比如，在程序启动的过程中需要从数据库中加载数据并缓存到程序的内存中

对此，我们可以在容器启动的过程中，通过依赖查找的方法获取到mappe，然后从数据库中获取数据并缓存到内存中，如下

```java
public class MainClass {

    public static ClassPathXmlApplicationContext context = null;

    private static CountDownLatch shutdownLatch = new CountDownLatch(1);

    public static void main(String[] args) throws Exception {
        // 加载spring上下文
        context = new ClassPathXmlApplicationContext(new String[]{"spring-config.xml"});
        context.start();
        // 从数据库获取数据并缓存到内存
        // 1.从容器中获取mapper
        ConfigMapper configMapper = (ConfigMapper) context.getBean("configMapper");
        // 2.从数据库中获取数据
        List<Config> RuleResultSet = configMapper.selectAll();
        // 3.将数据加载到PropertyMap中
        RuleResultSet.forEach(config -> PropertyMap.add(config.getName(), config.getValue()));
        context.registerShutdownHook();
        shutdownLatch.await();
    }
}
```

除此以外，我们还可以通过更加简洁的方式来实现，这就需要用到`@PostConstruct`注解

```java
@Component
public class InitConfigParameter {

    @Resource
    private ConfigMapper configMapper;
 
    @PostConstruct
    public void init() throws Exception {
        // 将数据库中的参数加载到哈希表中
        List<ItpcsConfig> RuleResultSet = configMapper.selectAll();
        RuleResultSet.forEach(config -> PropertyMap.add(config.getName(), config.getValue()));
    }
}

```

使用`@PostConstruct`注解修饰的init方法就会在Spring容器的启动时自动的执行，其具体实现原理可以参考https://juejin.cn/post/7010017313625735198

## @Retention、@Target

注解`@Target`和`@Retention`可以用来修饰注解，是注解的注解，称为元注解

### @Retention

@Retention作用是定义被它所注解的注解保留多久，一共有三种策略，定义在RetentionPolicy枚举中.

- `RetentionPolicy.SOURCE`：注解只保留在源文件，当Java文件编译成class文件的时候，注解被遗弃；被编译器忽略
- `RetentionPolicy.CLASS`：注解被保留到class文件，但jvm加载class文件时候被遗弃，这是默认的生命周期
- `RetentionPolicy.RUNTIME`：注解不仅被保存到class文件中，jvm加载class文件之后，仍然存在

这3个生命周期分别对应于：Java源文件(.java文件) ---> .class文件 ---> 内存中的字节码。

首先要明确生命周期长度 **SOURCE < CLASS < RUNTIME** ，所以前者能作用的地方后者一定也能作用。一般如果需要**在运行时去动态获取注解信息，那只能用 RUNTIME 注解**；如果要**在编译时进行一些预处理操作**，比如生成一些辅助代码（如 [ButterKnife](https://github.com/JakeWharton/butterknife)）**，就用 CLASS注解**；如果**只是做一些检查性的操作**，比如 **@Override** 和 **@SuppressWarnings**，则**可选用 SOURCE 注解**。

### @Target

目标，即该注解可以声明在哪些目标元素之前，也可理解为注释类型的程序元素的种类。

- `ElementType.PACKAGE`：用于描述包。 
- `ElementType.ANNOTATION_TYPE`：该注解只能声明在一个注解类型前。 
- `ElementType.TYPE`：用于描述类、接口(包括注解类型) 或enum声明
- `ElementType.CONSTRUCTOR`：用于描述类的构造器。
- `ElementType.LOCAL_VARIABLE`：用于描述局部变量。
- `ElementType.METHOD`：用于描述类的方法。
- `ElementType.PARAMETER`：用于描述方法参数。
- `ElementType.FIELD`：用于描述域(一个类的字段前).

### 定义注解

定义注解需使用 @interface关键词 ,并在接口上注明@Rentention(...) 或@Target(...) ，也可以把spring等注解加上来使用

```java
@Target({ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation{
    //定义final静态属性
    //定义抽象方法
}
```

比如：

```java
@Target({ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Transactional(value = "transactionManager", propagation = Propagation.REQUIRED, rollbackFor = Exception.class)
public @interface TestAnnotation{
 
}
```

注解的使用：

```java
@TestAnnotation
public String save(Person person){
}
```

