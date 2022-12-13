---
title: springboot学习(一):第一个项目以及原理浅析
categories: java
tags:
- springboot
- 框架
- 后端
---

# 1. 前言

## 学习历程

javase: 	OOP的思想

mysql:	 持久化

html+css+js+jquery+框架: 	视图层

javaweb: 	独立开发原始的MVC三层框架网站

SSM:  	框架，简化开发流程，但配置也开始复杂     

**war: tomcat运行**

spring再简化: 	SpringBoot-jar: 内嵌tomcat; 	微服务架构

服务越来越多: 	出现了springcloud



## Spring是怎么简化开发的

- 基于POJO的轻量级和最小侵入性编程，所有东西都是bean；
- 通过IOC，依赖注入（DI）和面向接口实现松耦合；
- 基于切面（AOP）和惯例进行声明式编程；3
- 通过切面和模版减少样式代码，RedisTemplate，xxxTemplate；

## 什么是SpringBoot

 一个javaweb的开发框架，和SpringMVC类似，对比其他javaweb框架的好处，官方说是简化开发，约定大于配置， you can “just run”，能迅速的开发web应用，几行代码开发一个http接口。

所有的技术框架的发展似乎都遵循了一条主线规律：从一个复杂应用场景衍生一种规范框架，人们只需要进行各种配置而不需要自己去实现它，这时候强大的配置功能成了优点；发展到一定程度之后，人们根据实际生产应用情况，选取其中实用功能和设计精华，重构出一些轻量级的框架；之后为了提高开发效率，嫌弃原先的各类配置过于麻烦，于是开始提倡**约定大于配置**，进而衍生出一些一站式的解决方案，这就是Java企业级应用->J2EE->spring->springboot的过程。

Spring Boot 基于 Spring 开发，Spirng Boot 本身并不提供 Spring 框架的核心特性以及扩展功能，只是用于快速、敏捷地开发新一代基于 Spring 框架的应用程序。也就是说，它并不是用来替代 Spring 的解决方案，而是和 Spring 框架紧密结合用于提升 Spring 开发者体验的工具。Spring Boot 以**约定大于配置的核心思想**，默认帮我们进行了很多设置，多数 Spring Boot 应用只需要很少的 Spring 配置。同时它集成了大量常用的第三方库配置（例如 Redis、MongoDB、Jpa、RabbitMQ、Quartz 等等），Spring Boot 应用中这些第三方库几乎可以零配置的开箱即用

简单来说就是SpringBoot其实不是什么新的框架，它默认配置了很多框架的使用方式，就像maven整合了所有的jar包，spring boot整合了所有的框架 。

**Spring Boot的主要优点：**

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

# 2.微服务阶段

## 单体应用架构

所谓单体应用架构(all in one)是指，我们将一个应用中的所有应用服务都封装在一个应用中

无论是ERP、CRM，都把数据库访问，web访问等功能发到一个war包内

- 好处：易于开发与测试；也十分方便部署；当需要拓展时，只需要将war复制多分，然后放到多个服务器上，再做个负载均衡就可以了
- 缺点：小的修改都需要停掉整个服务，重新打包、部署war包。如果是大型应用，如何维护、分工合作会成为大问题好处

## 微服务架构

打破之前all in one的架构方式，把各个功能元素独立出来，把独立出来的功能元素的动态组合，需要的功能元素才去组合，需要多一些时可以整合多个功能元素。所以微服务架构是对功能元素进行复制，而没有对整个应用进行复制。

这样做的好处：

- 节省了调用资源
- 每个功能元素的服务都是一个可替换的、可独立升级的软件代码

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211231651405.png)

[martinfowler的微服务论文](https://martinfowler.com/articles/microservices.html)

## 如何搭建微服务

一个大型系统的微服务架构，就像一个复杂交织的神经网络，每一个神经元就是一个功能元素，他们各自完成自己的功能，然后通过http相互请求调用。比如一个电商系统，查缓存、连数据库、浏览页面、结账、支付等服务都是一个个独立的功能服务，都被微化了，他们作为一个个微服务共同构建了一个完整的系统。如果要修改其中一个功能，只需要更新一个功能单元即可。

但是这种庞大的系统架构给部署和运维带来很大困难。于是，spring为我们带来了构建大型分布式微服务的全套、全程产品：

- 构建一个个功能独立的微服务应用单元，可以使用springboot，可以帮我们快速构建一个应用；
- 大型分布式网络服务的调用，这部分由springcloud来完成，实现分布式；
- 在分布式中间，进行流式数据计算、批处理，我们有spring cloud data flow
- spring为我们想清楚了整个从开始构建应用到大型分布式应用全流程方案

# 3.第一个springboot项目

## 准备工作

我们将学习如何快速的创建一个Spring Boot应用，并且实现一个简单的Http请求处理。通过这个例子对Spring Boot有一个初步的了解，并体验其结构简单、开发快速的特性。

## 创建基础项目说明

### 创建方式一：使用Spring Initializr

Spring官方提供了非常方便的工具让我们快速构建应用 [Spring Initializr](https://start.spring.io/)

![image-20221124150830774](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211241508921.png)

进入Spring Initializr页面，选择leaguage，project，springboot版本，项目信息，JDK版本以及依赖之后点击生成，得到一个压缩包，解压并用IDEA以Maven项目导入即可

### 创建方式二：使用 IDEA 直接创建项目

本质也是进入[Spring Initializr](https://start.spring.io/)，先填写项目信息



![image-20221124151841465](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211241518528.png)

再选择依赖，然后等待项目构建完成即可 

![image-20221124152056592](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211241520648.png)

## 项目结构分析

通过上面步骤完成了基础项目的创建。就会自动生成以下文件。

- 程序的主启动类，程序入口 SpringbootApplication.java
- 一个 application.properties 配置文件,但更推荐yml格式的配置文件
- 一个 测试类
- pom.xml

### pom.xml

打开pom.xml，看看Spring Boot项目的依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!--有一个父项目-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.5</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <!--g a v项目坐标-->
    <groupId>com.hanser</groupId>
    <artifactId>springboot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot</name>
    <description>springboot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--所有的SpringBoot依赖都是spring-boot-starter 开头的-->
        <!--web依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--单元测试依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--打包插件，在左侧maven里点击package即可再target目录下得到一个可执行jar包-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

### 基础配置

#### 端口号

再配置文件application.properties或者application.yml里配置

```properties
server.port=8081
```

```yaml
server:
  port: 8081
```

#### banner图案

![image-20221124210852460](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211242108518.png)

就是这个项目启动时的图案

- 在resources目录下新建一个banner.txt文件
- 在线生成自己想要的图案
- 将图案复制到banner.txt中

# 4.springboot自动装配原理浅析

## pom.xml(依赖)

我们发现本项目的父依赖spring-boot-starter-parent还有一个父依赖spring-boot-dependencies

![image-20221124212033786](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211242120840.png)

在spring-boot-dependencies里面有很多依赖的版本，这里是springboot的版本控制中心

![image-20221124212115052](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211242121112.png)

**以后我们导入依赖默认是不需要写版本；但是如果导入的包没有在依赖中管理着就需要手动配置版本了；**

### 启动器 spring-boot-starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

- SpringBoot的启动场景
- 比如spring-boot-starter-web,会帮我们自动导入web环境所有的依赖
- springboot会将所有功能场景,都变成一个个的启动器
- 我们要是用什么功能，就需要找到一个个启动器就可以了
- [under the org.springframework.boot group](https://docs.spring.io/spring-boot/docs/2.6.2/reference/htmlsingle/#using.build-systems.starters)

## 主启动类

```java
//@SpringBootApplication 标记这个类是一个springboot应用
@SpringBootApplication
public class SpringbootApplication {

    public static void main(String[] args) {
        //将springboot应用启动
        SpringApplication.run(SpringbootApplication.class, args);
    }

}
```

### 注解

点进@SpringBootApplication发现有三个主要注解

```
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```



#### @ComponentScan

就是一个包扫描注解，代表扫描主启动类所在目录下的所有包

#### @SpringBootConfiguration

这个注解下有一个@Configuration注解，点进去发现有@Component注解，代表他是一个spring的一个组件

#### @EnableAutoConfiguration

`自动配置核心注解`

##### @AutoConfigurationPackage

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
//导入一个自动注册包，扫描的包到这里注册
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
    
	String[] basePackages() default {};
    
	Class<?>[] basePackageClasses() default {};

}
```

##### @Import({AutoConfigurationImportSelector.class})

AutoConfigurationImportSelector：自动导入包的核心

- getAutoConfigurationEntry()方法：获得自动配置类的实体

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
		configurations = removeDuplicates(configurations);
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
		configurations.removeAll(exclusions);
		configurations = getConfigurationClassFilter().filter(configurations);
		fireAutoConfigurationImportEvents(configurations, exclusions);
		return new AutoConfigurationEntry(configurations, exclusions);
	}
```

- getCandidateConfigurations：获取候选的配置

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = new ArrayList<>(
				SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader()));
		ImportCandidates.load(AutoConfiguration.class, getBeanClassLoader()).forEach(configurations::add);
		Assert.notEmpty(configurations,
				"No auto configuration classes found in META-INF/spring.factories nor in META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports. If you "
						+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
```

- loadFactoryNames() ：获取所有的加载配置

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoader == null) {
        classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }

    String factoryTypeName = factoryType.getName();
    return (List)loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

- getSpringFactoriesLoaderFactoryClass：标注了EnableAutoConfiguration注解的类（主启动类）

```java
protected Class<?> getSpringFactoriesLoaderFactoryClass() {
   return EnableAutoConfiguration.class;
}
```

- loadSpringFactories()： 获取所有的配置

它做了一件事：遍历FACTORIES_RESOURCE_LOCATION下的所有资源，并把这些资源封装成Properties供我们使用

```java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
		Map<String, List<String>> result = cache.get(classLoader);
		if (result != null) {
			return result;
		}

		result = new HashMap<>();
		try {
			Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
			while (urls.hasMoreElements()) {
				URL url = urls.nextElement();
				UrlResource resource = new UrlResource(url);
				Properties properties = PropertiesLoaderUtils.loadProperties(resource);
				for (Map.Entry<?, ?> entry : properties.entrySet()) {
					String factoryTypeName = ((String) entry.getKey()).trim();
					String[] factoryImplementationNames =
							StringUtils.commaDelimitedListToStringArray((String) entry.getValue());
					for (String factoryImplementationName : factoryImplementationNames) {
						result.computeIfAbsent(factoryTypeName, key -> new ArrayList<>())
								.add(factoryImplementationName.trim());
					}
				}
			}

			// Replace all lists with unmodifiable lists containing unique elements
			result.replaceAll((factoryType, implementations) -> implementations.stream().distinct()
					.collect(Collectors.collectingAndThen(Collectors.toList(), Collections::unmodifiableList)));
			cache.put(classLoader, result);
		}
		catch (IOException ex) {
			throw new IllegalArgumentException("Unable to load factories from location [" +
					FACTORIES_RESOURCE_LOCATION + "]", ex);
		}
		return result;
	}
```

我们发现有这样一行

```java
Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
```

从FACTORIES_RESOURCE_LOCATION里获取资源，然后它是一个静态变量

```java
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
```

我们在对应lib目录下找到这个文件，所有的自动配置类都在这里

2.7.0版本之后自动配置类的配置位置变化，现在自动配置类需要放到文件`META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`中，该文件中每一行都是一个自动配置类的全路径名，其格式可以参考这个[官方文档](https://github.com/spring-projects/spring-boot/blob/main/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports)。不过为了保持向后兼容，SpringBoot依然会处理`spring.factories`文件中的自动配置类。

![image-20221125102915205](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251029302.png)

![image-20221125103432814](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251034890.png)

点进autoconfigure包，发现有不少autoConfiguration类，点进aop自动配置类，可以看到出现了大量的@ConditionalOnXXX注解，这是自动配置的核心注解，括号内的条件满足，这个类才会生效

![image-20221125105048193](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251050273.png)

## 总结

springboot的所有自动配置都是在启动时扫描并加载，`META-INF/spring.factories`里有不少自动配置类，但是不一定生效，大多数自动配置类都有一个叫@ConditionalOnXXX的注解，里面条件满足了，自动配置才会生效，我们才可以使用对应方法。

# 5.主启动类

了解一下主启动类怎么运行

## SpringApplication

这个类主要做了以下四件事情：(查看构造器)

- 推断应用的类型是普通的项目还是Web项目
- 查找并加载所有可用初始化器 ， 设置到initializers属性中
- 找出所有的应用程序监听器，设置到listeners属性中
- 推断并设置main方法的定义类，找到运行的主类

## run方法



![image-20220105134126771](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251451079.png)



# 6.yaml

## springboot配置文件

SpringBoot使用一个全局的配置文件 ， 配置文件名称是固定的(pom.xml里父项目规定的)

- application.properties
- application.yml
- application.yaml

配置文件的作用: 修改SpringBoot自动配置的默认值，因为SpringBoot在底层都给我们自动配置好了；

## yaml概述

YAML是 “YAML Ain’t a Markup Language” （YAML不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是：“Yet Another Markup Language”（仍是一种标记语言），**这种语言以数据作为中心，而不是以标记语言为重点！**

以前的配置文件，大多数都是使用xml来配置；比如一个简单的端口配置，我们来对比下yaml和xml

```xml
<server>
	<port>8081<port>
</server>
```

```yaml
server：
	prot: 8081
```

- .properties文件的形式是k=v(键值对)的形式
- .yml则是k: v的形式，冒号后面有空格

```yaml
# k-v键值对
# 相当于name=hanser
name: hanser

# 存对象
student:
  name: hanser
  age: 22
  
# 行内写法
student1: {name: hanser,age: 13}

# 数组
pets:
  - cat
  - dog
  - ppp

# 行内写法
pets1: [cat,dog,ppp]
```

注意：

- 字面量(数字，布尔值，字符串)直接写在后面就可以 ， 字符串默认不用加上双引号或者单引号；
- “ ” 双引号，会转义字符串里面的特殊字符 ， 特殊字符会作为本身想表示的意思；
- ‘’ 单引号，不会转义特殊字符 ， 特殊字符最终会变成和普通字符一样输出

## 注入配置文件

### yml注入配置文件(推荐)

yaml文件更强大的地方在于，他可以给我们的实体类或者配置类注入值

首先在yml文件中写上要配置的值，注意yml中的名称要和属性的的名称一致

```yaml
people:
  name: hanser
  age: 21
  happy: false
  birth: 2001/06/21
  map: {k1: v1,k2: v2}
  hobbies:
    - code
    - game
```

之后在对应实体类上加上@ConfigurationProperties，这时会报错，如下图，但是不影响测试

![image-20221125154210313](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251542371.png)

如果想削除提示,可以在pom.xml中加入这个依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

加入@ConfigurationProperties(prefix = "people")，prefix="yml中的对象名"，注解默认从applacation.yaml里取值

```java
@Data
@Component
@ConfigurationProperties(prefix = "people")
public class People {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map map;
    private List<Object> hobbies;
}
```

去测试类里测试，成功打印出信息

```java
@SpringBootTest
class SpringbootApplicationTests {

    @Resource
    private People people;
    @Test
    void contextLoads() throws SQLException {
        System.out.println(people);
    }

}
```

此外yml还支持各种形式的占位符

```yaml
people:
  name: hanser${random.uuid} # 随机uuid
  age: ${random.int}  # 随机int
  happy: false
  birth: 2001/06/21
  map: {k1: v1,k2: v2}
  hobbies:
    - code
    - game
  dog:
    name: ${people.hello:hanser}_旺财 #如果hello没有那么就hanser_旺财，否者就是hello的值_旺财
    age: 1
```



### 使用.properties文件注入配置(不推荐)

@PropertySource：加载指定的配置文件

我们去在resources目录下新建一个peopel.properties文件

```properties
name=hanser
```

注入实体类

```java
// 定义value值可以自定义properties文件
@Data
@Component
@PropertySource(value = "classpath:person.properties")
public class Person {
    //用el表达式取配置文件的值
    @Value("${name}")
    private String name;
}
```

### 对比

![image-20221125160248774](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251602828.png)

- @ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加
- 松散绑定： 比如我的yml中写的last-name，这个和lastName是一样的，-形式的会自动转化为驼峰命名。这就是松散绑定。
- JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性
- 复杂类型封装，yml中可以封装对象 ， 使用value就不支持

所以更推荐使用yml形式

# 7.JSR303检验

使用时，在对应类上加上@Validated注解代表启用数据校验

然后在对应属性上加入规则，规则有下面这个，需要详细查看可以点入引入的规则的包下查看源码

```
空检查 
@Null 验证对象是否为null 
@NotNull 验证对象是否不为null, 无法查检长度为0的字符串 
@NotBlank 检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格. 
@NotEmpty 检查约束元素是否为NULL或者是EMPTY.

Booelan检查 
@AssertTrue 验证 Boolean 对象是否为 true 
@AssertFalse 验证 Boolean 对象是否为 false

长度检查 
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内 
@Length(min=, max=) Validates that the annotated string is between min and max included.

日期检查 
@Past 验证 Date 和 Calendar 对象是否在当前时间之前，验证成立的话被注释的元素一定是一个过去的日期 
@Future 验证 Date 和 Calendar 对象是否在当前时间之后 ，验证成立的话被注释的元素一定是一个将来的日期 
@Pattern 验证 String 对象是否符合正则表达式的规则，被注释的元素符合制定的正则表达式，regexp:正则表达式 flags: 指定 Pattern.Flag 的数组，表示正则表达式的相关选项。

数值检查 
建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为“”时无法转换为int，但可以转换为Stirng为”“,Integer为null 
@Min 验证 Number 和 String 对象是否大等于指定的值 
@Max 验证 Number 和 String 对象是否小等于指定的值 
@DecimalMax 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度 
@DecimalMin 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度 
@Digits 验证 Number 和 String 的构成是否合法 
@Digits(integer=,fraction=) 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。 
@Range(min=, max=) 被指定的元素必须在合适的范围内 
@Range(min=10000,max=50000,message=”range.bean.wage”) 
@Valid 递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证) 
@CreditCardNumber信用卡验证 
@Email 验证是否是邮件地址，如果为null,不进行验证，算通过验证。 
@ScriptAssert(lang= ,script=, alias=) 
@URL(protocol=,host=, port=,regexp=, flags=)]()]()
```

# 8.多环境配置

profile是Spring对不同环境提供不同配置功能的支持，可以通过激活不同的环境版本，实现快速切换环境；

## 多配置文件

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml , 用来指定多个环境版本；

比如：

- application-test.properties 代表测试环境配置
- application-dev.properties 代表开发环境配置

但是Springboot并不会直接启动这些配置文件，它**默认使用application.properties主配置文件**；

我们需要通过一个配置来选择需要激活的环境：在主配置文件里加入

```properties
spring.profiles.active=dev
```

## yaml的多配置文件

和properties配置文件中一样，但是使用yml去实现不需要创建多个配置文件，更加方便了 !

```yaml
server:
  port: 8081
spring:
  profiles:
    active: dev  #代表dev配置文件生效
    
# 用三个短横线隔开的代表两个配置文件
---
server:
  port: 8082
spring:
  profiles: dev #命名为dev
    
---
server:
  port: 8083
spring:
  profiles: test #命名为test

```

**注意：如果yml和properties同时都配置了端口，并且没有激活其他环境 ， 默认会使用properties配置文件的！**

## 配置文件位置

![image-20221125195110727](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251951803.png)

./代表项目目录，且四个位置配置文件的优先级是从上到下递减的，高优先级的配置会覆盖低优先级的配置

# 9.自动配置原理再理解

## 我们能配什么

前面我们知道了springboot是如何进行自动装配的，核心注解是@EnableAutoConfiguration，并且深入到了MATE-INF/spring.factories的一些自动配置类，自动配置类又通过@ConditionalOnXXX注解来确定是否生效，那么以上是Springboot的自动配置，我们怎么能够知道在配置文件里究竟能自定义配置那些东西呢，这就需要我们更深入的进入自动配置类去寻找答案

我们以一个较为简单的HttpEncoding自动配置类作为例子

```java
// 和@Configuration不太一样，是2.7.0之后的新注解。该注解用于取代@Configuration注解，
// 用于解析SPI文件META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports中配置的自动配置类。
@AutoConfiguration
// 启动指定类的ConfigurationProperties功能；
// 进入这个ServerProperties查看，将配置文件中对应的值和ServerProperties绑定起来；
// 并把ServerProperties加入到ioc容器中
@EnableConfigurationProperties(ServerProperties.class)
// 这个自动配置类生效的三个条件
// 判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
// 判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnClass(CharacterEncodingFilter.class)
// 判断配置文件中是否存在某个配置：spring.http.encoding.enabled；
// 如果不存在，就把这个属性设置为true
@ConditionalOnProperty(prefix = "server.servlet.encoding", value = "enabled", matchIfMissing = true)
public class HttpEncodingAutoConfiguration {

	private final Encoding properties;

	public HttpEncodingAutoConfiguration(ServerProperties properties) {
		this.properties = properties.getServlet().getEncoding();
	}

	@Bean
    // 上下文不存在bean时进行
	@ConditionalOnMissingBean
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Encoding.Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Encoding.Type.RESPONSE));
		return filter;
	}

	@Bean
	public LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() {
		return new LocaleCharsetMappingsCustomizer(this.properties);
	}

	static class LocaleCharsetMappingsCustomizer
			implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>, Ordered {

		private final Encoding properties;

		LocaleCharsetMappingsCustomizer(Encoding properties) {
			this.properties = properties;
		}

		@Override
		public void customize(ConfigurableServletWebServerFactory factory) {
			if (this.properties.getMapping() != null) {
				factory.setLocaleCharsetMappings(this.properties.getMapping());
			}
		}

		@Override
		public int getOrder() {
			return 0;
		}

	}

}
```



点进ServerProperties中查看,刚点进去就发现了熟悉的@ConfigurationProperties注解，所以首先是server开头的

```java
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
```

之后去寻找我们要找的encoding设置

![image-20221125204940442](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211252049511.png)

![image-20221125204955497](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211252049545.png)

发现encoding是一个内部类对象servlet的一个属性，所以我们推测yaml的格式应该为

```yaml
server:
  servlet:
    encoding:
```

![image-20221125205454401](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211252054456.png)

结果也证明了我们是正确的

## 总结

- XXXAutoConfiguration是一个个的自动配置类
- 自动配置类根据一个或多个@ConditionalOnXXX注解来判断这个类是否生效
- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的
- 每一个priperties类又跟配置文件绑定(@ConfigurationProperties)
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；

**这就是自动装配的原理**！

## 精髓

- SpringBoot启动会加载大量的自动配置类
- 我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；
- 我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）
- 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

## 查看生效的自动配置

在yml文件中debug: true 

即可查看生效的(Positive matches)、未生效的自动配置类(Negative matches)、Exclusions、Unconditional classes

