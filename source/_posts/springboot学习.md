---
title: springboot学习(一):第一个项目以及原理浅析
categories: java
tags:
- springboot
- 框架
- 后端
---

#  前言

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

# 微服务阶段

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

# 第一个springboot项目

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

# springboot自动装配原理浅析

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

![image-20221125102915205](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251029302.png)

![image-20221125103432814](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251034890.png)

点进autoconfigure包，发现有不少autoConfiguration类，点进aop自动配置类，可以看到出现了大量的@ConditionalOnXXX注解，这是自动配置的核心注解，括号内的条件满足，这个类才会生效

![image-20221125105048193](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211251050273.png)

## 总结

springboot的所有自动配置都是在启动时扫描并加载，`META-INF/spring.factories`里有不少自动配置类，但是不一定生效，大多数自动配置类都有一个叫@ConditionalOnXXX的注解，里面条件满足了，自动配置才会生效，我们才可以使用对应方法。
