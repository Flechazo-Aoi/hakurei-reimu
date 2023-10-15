---
title: springboot学习(三):连接数据库
categories: 前后端框架学习
date: 2022-12-22  11:22:33
tags: 
- springboot
- 框架
---

 # 整合JDBC

## Spring Data简介

对于数据访问层(dao),无论是SQL还是NOSQL，SpringBoot底层都是采用Spring Data的方式进行统一处理

Spring Boot 底层都是采用 Spring Data 的方式进行统一处理各种数据库，Spring Data 也是 Spring 中与 Spring Boot、Spring Cloud 等齐名的知名项目。

[Sping Data 官网](https://spring.io/projects/spring-data)     [数据库相关启动器](https://docs.spring.io/spring-boot/docs/2.7.5/reference/htmlsingle/#using.build-systems.starters)

## jdbc

我们在配置文件中配置数据源

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/hanser?useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
```

根据之前学的，肯定有一个xxxxProperties和xxxxAutoConfiguration,前者里的属性就是我们在配置文件里能配的，后者则负责进行自动装配，我们搜索DatasourceProperties或者点进username，找到了配置文件，这里面就是我们能进行配置的属性

测试一下

```java
@SpringBootTest
class JdbcApplicationTests {

    @Resource
    DataSource dataSource;

    @Test
    void contextLoads() throws SQLException {
        // 查看默认的数据源
        System.out.println(dataSource.getClass());//class com.zaxxer.hikari.HikariDataSource

        // 获取数据库连接
        Connection connection = dataSource.getConnection();
        
        System.out.println(connection);

        // 关闭
        connection.close();
    }
}
```

我们注意到springboot的默认数据源是hikari，另外我们去配置文件里找发现还有Tomcat，Dbcp2，OracleUcp，Generic几种数据源

![image-20221129104045797](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211291040894.png)

**HikariDataSource 号称 Java WEB 当前速度最快的数据源，相比于传统的 C3P0 、DBCP、Tomcat jdbc 等连接池更加优秀；可以使用 spring.datasource.type 指定自定义的数据源类型，值为要使用的连接池实现的完全限定名。**

## JdbcTemplate

springboot中配置了大量的模板类，我们只要进行了配置，这些模板类会被自动注入容器，我们如果需要，直接从容器中取即可，JdbcTemplate就是Spring 对原生的JDBC 做的轻量级的封装

- 有了数据源(com.zaxxer.hikari.HikariDataSource)，然后可以拿到数据库连接(java.sql.Connection)，有了连接，就可以使用原生的 JDBC 语句来操作数据库；
- 即使不使用第三方第数据库操作框架，如 MyBatis等，Spring 本身也对原生的JDBC 做了轻量级的封装，即JdbcTemplate。
- 数据库操作的所有 CRUD 方法都在 JdbcTemplate 中。
- Spring Boot 不仅提供了默认的数据源，同时默认已经配置好了 JdbcTemplate 放在了容器中，程序员只需自己注入即可使用
- JdbcTemplate 的自动配置是依赖 org.springframework.boot.autoconfigure.jdbc 包下的 JdbcTemplateConfiguration 类

**JdbcTemplate主要提供以下几类方法：**

- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

## 测试CRUD

```java
package com.example.hanser.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;
import java.util.Map;

@RestController
public class JDBCController {
    @Autowired
    JdbcTemplate jdbcTemplate;

    //查询数据库的所有信息
    //没有实体类，数据库中的东西如何获取 -> map
    @GetMapping("/userList")
    public List<Map<String,Object>> userList(){
        String sql = "select * from user";
        List<Map<String,Object>> List_map= jdbcTemplate.queryForList(sql); //查询
        return List_map;

    }

    @GetMapping("/ADD")
    public String addUser(){
        String sql = "insert into user(id,name,pwd) values(6,'xioa','1234567')";
        jdbcTemplate.update(sql);
        return  "update-ok";
        //自动提交了事务
    }
    @GetMapping("/update/{id}")
    public String update(@PathVariable("id") int id){
        String sql = "update user set name = ? ,pwd= ?where id ="+id;
        //封装
        Object[] objects = new Object[2];
        objects[0] = "qq";
        objects[1] = "12345678";
        jdbcTemplate.update(sql,objects);
        return "update-ok2";
    }

    @GetMapping("/delete/{id}")
    public String delete(@PathVariable("id") int id){
        String sql = "delete from user where id = ?";
        jdbcTemplate.update(sql,id);
        return "delet-ok";
    }
}

```

# 集成Druid

## Druid简介

Java程序很大一部分要操作数据库，为了提高性能操作数据库的时候，又不得不使用数据库连接池。

Druid 是阿里巴巴开源平台上一个`数据库连接池实现`，结合了 C3P0、DBCP 等 DB 池的优点，同时加入了日志监控。

Druid 可以很好的监控 DB 池连接和 SQL 的执行情况，天生就是针对监控而生的 DB 连接池。

Druid已经在阿里巴巴部署了超过600个应用，经过一年多生产环境大规模部署的严苛考验。

Spring Boot 2.0 以上默认使用 Hikari 数据源，可以说 Hikari 与 Driud 都是当前 Java Web 上最优秀的数据源，我们来重点介绍 Spring Boot 如何集成 Druid 数据源，如何实现数据库监控。

[github地址](https://github.com/alibaba/druid/)

## 参数详解

![image-20221130153637248](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211301536403.png)

## 配置Druid数据源

1、添加上 Druid 数据源依赖。

```xml

<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>
```

2、切换数据源。之前已经说过 Spring Boot 2.0 以上默认使用 com.zaxxer.hikari.HikariDataSource 数据源，但可以 通过 spring.datasource.type 指定数据源。

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源
```

3、数据源切换之后，在测试类中注入 DataSource，然后获取到它，输出一看便知是否成功切换；

```java
System.out.println(dataSource.getClass());//class com.zaxxer.hikari.HikariDataSource
```

4、切换成功！既然切换成功，就可以设置数据源连接初始化大小、最大连接数、等待时间、最小连接数 等设置项；可以查看源码

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/hanser?useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源


    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

5、导入Log4j 的依赖

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

6、现在需要程序员自己为 DruidDataSource 绑定全局配置文件中的参数，再添加到容器中，而不再使用 Spring Boot 的自动生成了；我们需要 自己添加 DruidDataSource 组件到容器中，并绑定属性：

在config文件夹下创建DruidConfig

```java
//代表这是一个配置类
@Configuration
public class DruidConfig {
    /*
       将自定义的 Druid数据源添加到容器中，不再让 Spring Boot 自动创建
       绑定全局配置文件中的 druid 数据源属性到 com.alibaba.druid.pool.DruidDataSource从而让它们生效
       @ConfigurationProperties(prefix = "spring.datasource")：作用就是将 全局配置文件中
       前缀为 spring.datasource的属性值注入到 com.alibaba.druid.pool.DruidDataSource 的同名参数中
     */
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource() {
        return new DruidDataSource();
    }
}
```

7、去测试类里测试配置是否成功

```java
DruidDataSource druidDataSource = (DruidDataSource) dataSource;
System.out.println("druidDataSource 数据源最大连接数：" + druidDataSource.getMaxActive());
System.out.println("druidDataSource 数据源初始化连接数：" + druidDataSource.getInitialSize());
```

`druidDataSource 数据源最大连接数：20
 druidDataSource 数据源初始化连接数：5`，可见配置已经生效

## 配置Druid数据源监控

Druid 数据源具有监控的功能，并提供了一个 web 界面方便用户查看，类似安装 路由器 时，人家也提供了一个默认的 web 页面。

所以第一步需要设置 Druid 的后台管理页面，比如 登录账号、密码 等；配置后台管理；

```java
//配置 Druid 监控管理后台的Servlet；
//内置 Servlet 容器时，没有web.xml文件，所以使用 Spring Boot 的注册 Servlet 方式
@Bean
public ServletRegistrationBean statViewServlet() {
    ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");

    // 这些参数可以在 com.alibaba.druid.support.http.StatViewServlet
    // 的父类 com.alibaba.druid.support.http.ResourceServlet 中找到
    Map<String, String> initParams = new HashMap<>();
    initParams.put("loginUsername", "admin"); //后台管理界面的登录账号
    initParams.put("loginPassword", "123456"); //后台管理界面的登录密码

    //后台允许谁可以访问
    //initParams.put("allow", "localhost")：表示只有本机可以访问
    //initParams.put("allow", "")：为空或者为null时，表示允许所有访问
    initParams.put("allow", "");
    //deny：Druid 后台拒绝谁访问
    //initParams.put("hanser", "192.168.1.20");表示禁止此ip访问

    //设置初始化参数
    bean.setInitParameters(initParams);
    return bean;
}
```

配置完成后，我们可以选择访问 ：http://localhost:8080/druid

![image-20221130163604484](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211301636534.png)

输入我们上面设置的用户名和密码，即可进入，在这里可以查看详细的日志，包括执行时间，事务等等

![image-20221130163743506](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202211301637597.png)

**配置 Druid web 监控 filter 过滤器**

```java
//配置 Druid 监控 之  web 监控的 filter
//WebStatFilter：用于配置Web和Druid数据源之间的管理关联监控统计
@Bean
public FilterRegistrationBean webStatFilter() {
    FilterRegistrationBean bean = new FilterRegistrationBean();
    bean.setFilter(new WebStatFilter());

    //exclusions：设置哪些请求进行过滤排除掉，从而不进行统计
    Map<String, String> initParams = new HashMap<>();
    initParams.put("exclusions", "*.js,*.css,/druid/*,/jdbc/*");
    bean.setInitParameters(initParams);

    //"/*" 表示过滤所有请求
    bean.setUrlPatterns(Arrays.asList("/*"));
    return bean;
}
```

平时在工作中，按需求进行配置即可，主要用作监控！

# 整合mybatis

## 导入依赖

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>
```

## 连接数据库

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/hanser?useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
```

## 在yml文件中进行mybatis的配置

```yaml
mybatis:
  # 别名	
  type-aliases-package: com.hanser.pojo
  # mapper文件location
  mapper-locations:
    - classpath:mybatis/mapper/*.xml
  configuration:
    # 自动转换为驼峰命名
    map-underscore-to-camel-case: true
    # 日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    # 开启二级缓存
    cache-enabled: true
```

## 编写实体类User

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
public class User {
    private int id;
    private String name;
    private String password;
}
```

## 编写Mapper接口和Mapper.xml

```java
// @Mapper表示这是一个mybatis的mapper类
@Mapper
// @Repository 将他注册进容器中，方便别的层调用
@Repository
public interface UserMapper {
    int addUser(User user);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hanser.mapper.UserMapper">
    <insert id="addUser" parameterType="user">
        insert into user (name, password)
        VALUES (#{name}, #{password})
    </insert>
</mapper>
```

## 编写controller类测试

```java
@RestController
public class UserController {
    @Resource
    UserMapper userMapper;

    @RequestMapping("/add")
    public int addUser(){
        User user = new User();
        user.setName("hanser");
        user.setPassword("123456");
        return userMapper.addUser(user);
    }
}
```

