---
title: springboot学习(五)：swagger
categories: 前后端框架学习
date: 2022-12-23  11:22:33
tags: 
- springboot
- 框架
---

## 前言

### 前后端分离开发

- 前端 -> 前端控制层、视图层
- 后端 -> 后端控制层、服务层、数据访问层
- 前后端通过API进行交互
- 前后端相对独立且松耦合
- 前后端可以部署在不同的服务器上

**产生的问题**：前后端集成，前端或者后端无法做到“及时协商，尽早解决”，最终导致问题集中爆发

**解决方案**：首先定义schema [ 计划的提纲 ]，并实时跟踪最新的API，降低集成风险

### swagger

- 号称世界上最流行的API框架
- Restful Api 文档在线自动生成器 => **API 文档 与API 定义同步更新**
- 直接运行，在线测试API
- 支持多种语言 （如：Java，PHP等）
- [官网](https://swagger.io/)

## SpringBoot集成Swagger

### 导入依赖

如果你要使用swagger2，则需要导入两个依赖

```xml
 <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
```

如果你要是用swagger3，则只需要导入一个starter即可

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

如果使用的是swagger3，则启动项目会发生Failed to start bean‘documentationPluginsBootstrapper错误，这是因为2.5.6以上版本的springboot与swagger3冲突，降级或者在配置文件里加入一行配置即可解决

```yaml
spring:
  mvc:
    pathmatch:
      matching-strategy: ANT_PATH_MATCHER
```

这样我们就能正常启动

- 如果使用的是swagger3，可以直接进入`http://localhost:8080/swagger-ui/index.html`，因为有默认的配置
- 如果使用的是swagger2，我们需要自定义一个配置类，并加上注解才能进入`http://localhost:8080/swagger-ui.html`

![image-20221213170203357](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212131702424.png)

### 自定义配置类

我们创建一个配置类并加上这两个注释就生成了我们的自定义配置类

```java
@Configuration //说明这是一个配置类
//开启Swagger,如果是swagger2，则是@EnableSwagger2
@EnableOpenApi
public class SwaggerConfig {
}
```

#### 修改swagger信息

```java
//配置Swagger信息 --> apiInfo
public ApiInfo apiInfo(){
    //作者信息
    Contact contact = new Contact("hanser","https://www.hakurei-reimu.love/","159753852654");
    return new ApiInfo(
            "hanser",   //
            "Hakurei Reimu", "1.0",
            "https://www.hakurei-reimu.love/", contact,
            "Apache 2.0", "http://www.apache.org/licenses/LICENSE-2.0", new ArrayList());
}

@Bean
// 自定义的配置，链式编程
public Docket publicApi() {
    //如果是swagger则需要return new Docket(DocumentationType.SWAGGER_2)
    return new Docket(DocumentationType.OAS_30)
            .apiInfo(apiInfo());
}
```

这样我们再次进入，就能看到新的自定义信息

![image-20221213170704063](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212131707105.png)

#### 配置显示的接口信息

```java
@Bean
// 自定义的配置，链式编程
public Docket publicApi() {
    //配置了SwaggerBean实例
    return new Docket(DocumentationType.OAS_30)
            .apiInfo(apiInfo())
            //enable默认为true，设为false时关闭swagger功能
            .enable(true)
            .select()
            /* 扫描的包
            * RequestHandlerSelectors 要扫描接口的方式
            * 指定要扫描的包 basePackage()
            * any() 扫描全部
            * withClassAnnotation(RestController.class) 扫描类上的注解，需要注解的反射对象
            * withMethodAnnotation() 扫描方法上注解
            * */
            .apis(RequestHandlerSelectors.basePackage("com.hanser.controller"))
            /* 过滤请求路径
             * PathSelectors 过滤的方式
             * any()  所有的都要
             * none() 所有的都不要
             * regex() 参数为一个字符串,字符串为一个正则表达式
             * ant() 参数为一个字符串,ant("/hello/**")代表显示所有localhost:8080/hello/**的请求
             * */
            .paths(PathSelectors.ant("/hello/**"))
            .build();
}
```

如果我们想要整多个组，只需创建多个Docket对象即可(但是真实开发环境都是一人一个，且都会注册到容器中)

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.OAS_30)
            .groupName("Pachouli");
}
@Bean
public Docket docket2(){
    return new Docket(DocumentationType.OAS_30)
            .groupName("Knowledge");
}
```

#### 多环境

配置了swagger之后，我们发现我们启动变慢了，所以在发布环境中，是要关闭swagger的，只在生产环境中开启swagger

```java
@Bean
@Profile("dev")
// 代表这个方法只会在环境为dev时执行
public Docket docket1(){
    // 我们可以通过注入Environment对象，然后.getProperty("flag")获取配置文件中flag的值,返回值为String
    // boolean flag = environment.getProperty("flag").equals("true");
    return new Docket(DocumentationType.OAS_30)
            .groupName("Pachouli");
}
@Bean
@Profile("prod")
public Docket docket2(){
    return new Docket(DocumentationType.OAS_30)
            .enable(false)
            .groupName("Knowledge");
}
```

关于多环境配置可以参考[springboot 学习 (一): 第一个项目以及原理浅析](https://www.hakurei-reimu.love/2022/12/08/springboot%E5%AD%A6%E4%B9%A0/)里的第八节

#### 注解：

配置完成之后，就是要进行给实体类或者接口、接口方法加上描述性语句

使用注解，而且因为改动太大，故 springfox对旧版的 swagger做了兼容处理。
但不知道未来会不会不兼容，这里列出如何用 swagger 3 的注解（已经在上面引入）代替 swagger 2 的
(注意修改 swagger 3 注解的包路径为io.swagger.v3.oas.annotations.)

| swagger2           | **OpenAPI 3**                                                | **注解位置**                       |
| ------------------ | ------------------------------------------------------------ | ---------------------------------- |
| @Api               | **@Tag(name = “接口类描述”)**                                | Controller 类上                    |
| @ApiOperation      | **@Operation(summary =“接口方法描述”)**                      | Controller 方法上                  |
| @ApiImplicitParams | **@Parameters**                                              | Controller 方法上                  |
| @ApiImplicitParam  | **@Parameter(description=“参数描述”)**                       | Controller 方法上 `@Parameters` 里 |
| @ApiParam          | **@Parameter(description=“参数描述”)**                       | Controller 方法的参数上            |
| @ApiIgnore         | **@Parameter(hidden = true)** 或 **@Operation(hidden = true)** 或 **@Hidden** | -                                  |
| @ApiModel          | **@Schema**                                                  | DTO类上                            |
| @ApiModelProperty  | **@Schema**                                                  | DTO属性上                          |

Swagger2 的注解命名以易用性切入，全是 Api 开头，在培养出使用者依赖注解的习惯后，Swagger 3将注解名称规范化，工程化。



如果想要从swagger2切换到swagger3，可以参考[Swagger3 注解使用（Open API 3）](https://blog.csdn.net/qq_35425070/article/details/105347336)
