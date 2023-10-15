---
title: springboot学习(四)：安全问题
categories: 前后端框架学习
date: 2022-12-22  15:22:33
tags: 
- springboot
- 框架
---





在web开发中，安全应该放在第一位：拦截器，过滤器，且安全问题应该在**设计之初**考虑

有两个框架：Spring Security、shiro，两个框架很像

# SpringSecurity

## 简介

- Sprin gSecurity是Springboot底层安全模块默认的技术选型，它可以实现强大的Web安全机制，只需要少数的`spring-boot--spring-security`依赖，进行少量的配置，就可以实现

- 功能权限、访问权限、菜单权限…，我们使用过滤器，拦截器需要写大量的原生代码，这样很不方便
- 所以在网址设计之初，就应该考虑到权限验证的安全问题，其中Shiro、SpringSecurity使用很多

## 搭建环境

### 导入静态资源

[提取码：d4hu](https://pan.baidu.com/s/1ueh1BD2MSRtSrD0Y_Wakcg)

### 编写controller层的视图控制

```java
@Controller
public class RouterController {
    @RequestMapping({"/","/index"})
    public String index(){
        return "index";
    }

    @RequestMapping("/toLogin")
    public String toLogin(){
        return "views/login";
    }

    //可以实现公用
    @RequestMapping("/level1/{id}")
    public String level(@PathVariable("id") int id){
        return "views/level1/" +id;
    }

    @RequestMapping("/level2/{id}")
    public String level2(@PathVariable("id") int id){
        return "views/level2/" +id;
    }

    @RequestMapping("/level3/{id}")
    public String level3(@PathVariable("id") int id){
        return "views/level3/" +id;
    }

}
```

### 测试环境

进入该页面说明环境搭建成功

![image-20221203170653269](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212031707387.png)

**注意**：如果导入了SpringSecurity依赖，则系统会自动拦截请求，并进入框架自带的login页面，用户名默认为user，密码则在控制台自动生成`Using generated security password: 5b45de9f-xxxx-xxxx-xxxx-deb8645f1b81`，登录后即可进入

![image-20221203170856557](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212031708599.png)

## 实现用户认证与授权

记住几个类 ：

- WebSecurityConfigurerAdapter:自定义Security策略，也就是我们要重写的类
- AuthenticationManagerBuilder:自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式

Spring Security的两个主要目标是 “认证” 和 “授权”（访问控制）

**认证(Authentication)**：

- 身份验证是关于验证您的凭据，如用户名/用户ID和密码，以验证您的身份。

- 身份验证通常通过用户名和密码完成，有时与身份验证因素结合使用。

**授权(Authorization)**：

授权发生在系统成功验证您的身份后，最终会授予您访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。

这个概念是通用的，而不是只在Spring Security 中存在。

实现：

### 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 编写配置类

[参考官网](https://spring.io/projects/spring-security)

### 定义授权规则：

```java
@EnableWebSecurity // 开启WebSecurity模式
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //链式编程
    //定制请求的授权规则
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 首页所有人可以访问,vip界面只有对应的人能访问
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/views/level1/**").hasRole("vip1")
                .antMatchers("/views/level2/**").hasRole("vip2")
                .antMatchers("/views/level3/**").hasRole("vip3");
    }
}
```

我们重启后发现，首页所有人都能进去，vip界面不再能进去(403错误，没有权限)，如果我们想要让没有权限自动跳转到登陆界面，可以在下面加上,

```java
// 开启自动配置的登录功能
// /login 请求来到登录页
// /login?error 重定向到这里表示登录失败
http.formLogin();
```

### 定义认证规则：

```java
//定义认证规则
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //在内存中定义，也可以在jdbc中去拿....
    auth.inMemoryAuthentication()
            .withUser("hanser").password("123456").roles("vip2","vip3")
            .and()
            .withUser("root").password("123456").roles("vip1","vip2","vip3")
            .and()
            .withUser("guest").password("123456").roles("vip1","vip2");
}
```

然后我们登录，出现了500错误，这是因为我们要将前端传过来的密码进行某种方式加密，否则就无法登录，

```java
//定义认证规则
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    //在内存中定义，也可以在jdbc中去拿....
    //Spring security 5.0中新增了多种加密方式，也改变了密码的格式。
    //要想我们的项目还能够正常登陆，需要修改一下configure中的代码。我们要将前端传过来的密码进行某种方式加密
    //spring security 官方推荐的是使用bcrypt加密方式。
    auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
            .withUser("hanser").password(new BCryptPasswordEncoder().encode("123456")).roles("vip2", "vip3")
            .and()
            .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2", "vip3")
            .and()
            .withUser("guest").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2");
}
```

登录测试，发现不在出错

至此，我们就实现了用户认证与授权

# Shiro

## 概述

### 什么是Shiro

Apache Shiro 是一个功能强大且易于使用的 Java 安全(权限)框架。Shiro 可以完成：认证、授权、加密、会话管理、与 Web 集成、缓存 等。借助 Shiro 您可以快速轻松地保护任何应用程序——从最小的移动应用程序到最大的 Web 和企业应用程序。

### 为什么要用Shiro

自 2003 年以来，框架格局发生了相当大的变化，因此今天仍然有很多系统在使用 Shiro。这与 Shiro 的特性密不可分。

- 易于使用：使用 Shiro 构建系统安全框架非常简单。就算第一次接触也可以快速掌握。
- 全面：Shiro 包含系统安全框架需要的功能，满足安全需求的“一站式服务”。
- 灵活：Shiro 可以在任何应用程序环境中工作。虽然它可以在 Web、EJB 和 IoC 环境中工作，但不需要依赖它们。Shiro 也没有强制要求任何规范，甚至没有很多依赖项。
- 强力支持 Web：Shiro 具有出色的 Web 应用程序支持，可以基于应用程序 URL 和 Web 协议（例如 REST）创建灵活的安全策略，同时还提供一组 JSP 库来控制页面输出。
- 兼容性强：Shiro 的设计模式使其易于与其他框架和应用程序集成。Shiro 与 Spring、Grails、Wicket、Tapestry、Mule、Apache Camel、Vaadin 等框架无缝集成。
- 社区支持：Shiro 是 Apache 软件基金会的一个开源项目，有完备的社区支持，文档支持。如果需要，像 Katasoft 这样的商业公司也会提供专业的支持和服务。

### 与 SpringSecurity 的对比

- Spring Security 基于 Spring 开发，项目若使用 Spring 作为基础，配合 Spring Security 做权限更加方便，而 Shiro 需要和 Spring 进行整合开发；
- Spring Security 功能比 Shiro 更加丰富些，例如安全维护方面；
- Spring Security 社区资源相对比 Shiro 更加丰富；
- Shiro 的配置和使用比较简单，Spring Security 上手复杂些；
- Shiro 依赖性低，不需要任何框架和容器，可以独立运行.Spring Security 依赖 Spring 容器；
- shiro 不仅仅可以使用在 web 中，它可以工作在任何应用环境中。在集群会话时 Shiro 最重要的一个好处或许就是它的会话是独立于容器的。

### 基本功能

![image-20221206151232023](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061512205.png)

- Authentication：身份认证/登录，验证用户是不是拥有相应的身份；
- Authorization：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用 户是否能进行什么操作，如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户 对某个资源是否具有某个权限；
- Session Manager：会话管理，即用户登录后就是一次会话，在没有退出之前，它的 所有 信息都在会话中；会话可以是普通 JavaSE 环境，也可以是 Web 环境的；
- Cryptography：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；
- Web Support：Web 支持，可以非常容易的集成到 Web 环境；
- Caching：缓存，比如用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可 以提高效率；
- Concurrency：Shiro 支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；
- Testing：提供测试支持；
- Run As：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；
- Remember Me：记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

### 架构原理

#### Shiro 架构(外部)

从外部来看 Shiro ，即从应用程序角度的来观察如何使用Shiro 完成工作

![image-20220927225903324](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061539761.png)

- Subject：应用代码直接交互的对象是 Subject，也就是说 Shiro 的对外 API 核心 就是 Subject。Subject 代表了当前“用户”， 这个用户不一定 是一个具体的人，与当 前应用交互的任何东西都是 Subject，如网络爬虫， 机器人等；与 Subject 的所有交互 都会委托给 SecurityManager； Subject 其实是一个门面，SecurityManager 才是实际的执行者；
- SecurityManager：安全管理器；即所有与安全有关的操作都会与 SecurityManager交互；且其管理着所有 Subject；可以看出它是 Shiro 的核心，它负责与 Shiro 的其他组件进行交互，它相当于 SpringMVC 中 DispatcherServlet 的角色
- Realm：Shiro 从 Realm 获取安全数据（如用户、角色、权限），就是说SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户 进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色/ 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource

#### shiro架构(内部)

![image-20220927230118046](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061541826.png)

- `Subject`：任何可以与应用交互的“用户”；
- `Cryptography`：密码模块，Shiro 提高了一些常见的加密组件用于如密码加密/解密。
- `SecurityManager` ：相当于 SpringMVC 中的 DispatcherServlet；是 Shiro 的心脏； 所有具体的交互都通过 SecurityManager 进行控制；它管理着所有 Subject、且负责进 行认证、授权、会话及缓存的管理。
  - `Authenticator`：负责 Subject 认证，是一个扩展点，可以自定义实现；可以使用认证策略（Authentication Strategy），即什么情况下算用户认证通过了；
  - `Authorizer`：授权器、即访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；
  - `Realm`：可以有 1 个或多个 Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是 JDBC 实现，也可以是内存实现等等；由用户提供；所以一般在应用中都需要实现自己的 Realm；
  - `SessionManager`：管理 Session 生命周期的组件；而 Shiro 并不仅仅可以用在 Web环境，也可以用在如普通的 JavaSE 环境
  - `CacheManager`：缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据 基本上很少改变，放到缓存中后可以提高访问的性能

## 初步使用

### 环境搭建

引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.9.0</version>
    </dependency>
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

伪造数据：ini文件

```ini
[users]
Sakura=Reimu
Izayoi=Sakuya
Pachouli=Knowledge
Kirisame=Marisa
Flandre=Scarlet
Remilia=Scarlet
```

### 实现登录认证

#### 登录认证概念

- 身份验证：一般需要提供如身份ID等一些标识信息来表明登录者的身份，如提供email，用户名/密码来证明。
- 在shiro中，用户需要提供principals（身份）和credentials（证明）给shiro，从而应用能验证用户身份：
- principals：身份，即主体的标识属性，可以是任何属性，如用户名、邮箱等，唯一即可。一个主体可以有多个principals，但只有一个Primary principals，一般是用户名/邮箱/手机号。
- credentials：证明/凭证，即只有主体知道的安全值，如密码/数字证书等。
- 最常见的principals和credentials组合就是用户名/密码

#### 登录认证基本流程

- 收集用户身份/凭证，即如用户名/密码

- 调用 Subject.login 进行登录，如果失败将得到相应 的 AuthenticationException异常，根据异常提示用户 错误信息；否则登录成功

- 也可以创建自定义的 Realm 类，继承 org.apache.shiro.realm.AuthenticatingRealm类,实现 doGetAuthenticationInfo() 方法

  ![image-20220927225324731](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061637318.png)

#### 示例

```java
@Test
public void login(){
    // 1.初始化获取SecurityManager
    IniSecurityManagerFactory factory = new IniSecurityManagerFactory("classpath:shiro.ini");
    SecurityManager securityManager = factory.getInstance();
    SecurityUtils.setSecurityManager(securityManager);
    // 2.获取subject对象
    Subject subject = SecurityUtils.getSubject();
    // 3.创建token对象，web应用用户名密码从页面传递
    UsernamePasswordToken token = new UsernamePasswordToken("Hakurei", "Reimu");
    // 4.完成登录
    try {
        subject.login(token);
        System.out.println("登录成功，token = " + token);
    } catch (UnknownAccountException e){
        e.printStackTrace();
        System.out.println("用户不存在");
    } catch (IncorrectCredentialsException e){
        e.printStackTrace();
        System.out.println("密码错误");
    }
}
```

发现在控制台输出了用户信息，这样我们就实现了登录认证

![image-20221206165556212](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061655254.png)

### 实现角色授权

#### 授权概念

**授权**，也叫访问控制，即在应用中控制谁访问哪些资源（如访问页面/编辑数据/页面 操作等）。在授权中需了解的几个关键对象：`主体（Subject）`、`资源（Resource）`、`权限 （Permission）`、`角色（Role）`。

- **主体(Subject)**：访问应用的用户，在 Shiro 中使用 Subject 代表该用户。用户只有授权 后才允许访问相应的资源。
- **资源(Resource)**：在应用中用户可以访问的 URL，比如访问 JSP 页面、查看/编辑 某些 数据、访问某个业务方法、打印文本等等都是资源。用户只要授权后才能访问。
- **权限(Permission)**：安全策略中的原子授权单位，通过权限我们可以表示在应用中用户 有没有操作某个资源的权力。即权限表示在应用中用户能不能访问某个资源，如：访问用 户列表页面查看/新增/修改/删除用户数据（即很多时候都是CRUD（增查改删）式权限控 制）等。权限代表了用户有没有操作某个资源的权利，即反映在某个资源上的操作允许不允许
- Shiro 支持`粗粒度权限`（如用户模块的所有权限）和`细粒度权限`（操作某个用户的权限， 即实例级别的）
- **角色(Role)**：`权限的集合`，一般情况下会赋予用户角色而不是权限，即这样用户可以拥有 一组权限，赋予权限时比较方便。典型的如：项目经理、技术总监、CTO、开发工程师等 都是角色，不同的角色拥有一组不同的权限

#### 查看授权方式

编程式：

```java
subject.hasRole("admin")
```

注解式：

```java
@RequiresRoles("admin")
```

JSP标签

```jsp
<shiro:hasRole name="admin">
    
</shiro:hasRole>
```

#### 查看授权流程

- 首先调用Subject.isPermitted(是否有某项权限)/hasRole(是否有某个角色)接口，其会委托给SecurityManager，而SecurityManager接着会委托给 Authorizer；
- Authorizer是真正的授权者，如果调用如isPermitted(“user:view”)，其首先会通过PermissionResolver把字符串转换成相应的Permission实例；
- 在进行授权之前，其会调用相应的Realm获取Subject相应的角色/权限用于匹配传入的角色/权限；
- Authorizer会判断Realm的角色/权限是否和传入的匹配，如果有多个Realm，会委托给ModularRealmAuthorizer进行循环判断，如果匹配如isPermitted/hasRole 会返回 true，否则返回false表示授权失败

#### 示例

首先修改ini文件，加入角色并给用户加上角色

```ini
[users]
Hakurei=Reimu,admin
Izayoi=Sakuya,admin
Pachouli=Knowledge,admin
Kirisame=Marisa,guest
Flandre=Scarlet,admin
Remilia=Scarlet,admin


[roles]
admin=user:insert,user:delete,user:update,user:select
guest=user:select
```

测试：

```java
@Test
public void authorization(){
    // 1.初始化获取SecurityManager
    IniSecurityManagerFactory factory = new IniSecurityManagerFactory("classpath:shiro.ini");
    SecurityManager securityManager = factory.getInstance();
    SecurityUtils.setSecurityManager(securityManager);
    // 2.获取subject对象
    Subject subject = SecurityUtils.getSubject();
    // 3.创建token对象，web应用用户名密码从页面传递
    UsernamePasswordToken token = new UsernamePasswordToken("Hakurei", "Reimu");
    // 4.完成登录
    subject.login(token);
    System.out.println(token);
    // 5.判断是登录的用户是否有某个角色
    System.out.println("admin:"+subject.hasRole("admin"));
    System.out.println("guest:"+subject.hasRole("guest"));
    // 6.判断登陆的用户时候有某种权限
    System.out.println("user:insert:"+subject.isPermitted("user:insert"));
    System.out.println("user:update:"+subject.isPermitted("user:update"));
    // 另一种方式判断时候有权限，但是没有返回结果，没有权限抛出AuthenticationException
    subject.checkPermission("user:update");
}
```

发现输出了角色和权限

![image-20221206172632957](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212061726008.png)

### 实现加密

实际系统开发中，一些敏感信息需要进行加密，比如说用户的密码。Shiro 内嵌很多 常用的加密算法，比如 MD5 加密。Shiro 可以很简单的使用信息加密。

```java
@Test
public void testMD5(){
    // 密码明文
    String password = "password";
    // 使用MD5加密
    Md5Hash md5Hash = new Md5Hash(password);
    System.out.println("md5Hash = " + md5Hash.toHex());
    // 带盐的MD5加密，带盐就是在密码明文后拼接字符串，然后在进行加密
    Md5Hash saltMd5Hash = new Md5Hash(password, "salt");
    System.out.println("saltMd5Hash = " + saltMd5Hash);
    // 为了避免被破解，还可以使用多次迭代加密，保证数据安全
    Md5Hash saltMd5Hash3 = new Md5Hash(password, "salt", 3);
    System.out.println("saltMd5Hash3 = " + saltMd5Hash3);
    // 如果想要自定义加密，可以使用Md5Hash的父类SimpleHash，然后自定义加密方式即可
    SimpleHash simpleHash = new SimpleHash("MD5", password, "salt", 3);
    System.out.println("simpleHash = " + simpleHash);
    assert simpleHash.equals(saltMd5Hash3);
}
```

### 自定义登录认证

Shiro 默认的登录认证是不带加密的，如果想要实现加密认证需要自定义登录认证，

可以创建自定义的 Realm 类，继承 org.apache.shiro.realm.AuthenticatingRealm类,实现 doGetAuthenticationInfo() 方法

```java
public class MyRealm extends AuthenticatingRealm {
    /*自定义的登录认证方法，定义了之后，Shiro 的  login 方法底层会调用该类的认证方法完成登录认证
     * 需要配置自定义的  realm 生效，在  ini 文件中配置，或Springboot中配置
     * 该方法只是获取进行对比的信息，认证逻辑还是按照Shiro的底层认证逻辑完成认证
     * @param token ： 令牌
     * @return {@link AuthenticationInfo}
     * @throws AuthenticationException 身份验证异常
     * */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        // 1.获取身份信息
        Object principal = token.getPrincipal();
        // 2.获取凭证信息
        Object credentials = new String((char[]) token.getCredentials());
        System.out.println("认证信息为："+principal+"---"+credentials);
        // 3.获取数据库中存储的用户信息,正常应该if里为查询语句查的对象不为空，pwd为数据库中存储的密文
        if ("Hakurei".equals(principal.toString())){
            String pwd = new Md5Hash("Reimu","salt",3).toHex();
            // 参数: 从数据库里查出来的用户名，从数据库里查出来的密文密码，加密用的盐，身份信息转化成字符串
            // 4.创建封装校验逻辑对象并返回
            return new SimpleAuthenticationInfo(principal,pwd, ByteSource.Util.bytes("salt"),principal.toString());
        }
        return null;
    }
}
```

之后还要再ini文件中加入配置信息，让shiro知晓你使用的是自定义的Realm

```ini
[main]
md5CredentialsMatcher=org.apache.shiro.authc.credential.Md5CredentialsMatcher
md5CredentialsMatcher.hashIterations=3

myrealm=shiro.MyRealm
myrealm.credentialsMatcher=$md5CredentialsMatcher
securityManager.realms=$myrealm
```

## 整合Springboot

### 环境搭建

导入依赖

```xml
<dependencies>
    <!--shiro-->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-spring-boot-web-starter</artifactId>
        <version>1.9.0</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.3.0</version>
    </dependency>
    <!--mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.46</version>
    </dependency>
    <!--thymeleaf-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

编写配置文件

```yaml
# mybatis配置
mybatis:
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    cacheEnabled: true
  type-aliases-package: com.hanser.pojo
  mapper-locations: classpath:mapper/*.xml
  
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/user?characterEncoding=UTF-8&serverTimezone=GMT&useUnicode=true&useSSL=false

  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8


shiro:
# 登录接口
  loginUrl: /login
```

准备好数据库，我这里的为hanser库下的一个user表

![image-20221207201431334](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212072015125.png)

### 登录认证实现

#### 后端接口实现

首先按照这个项目结构建好包和类

![image-20221207201307772](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212072013877.png)

##### 实体类

使用lombok插件快速生成各种方法

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
    private int rid;
}
```

##### dao层:

`UserMapper`

```java
@Mapper
@Repository
public interface UserMapper {
    User login(@Param("name") String name);
}
```

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hanser.mapper.UserMapper">
    <select id="login" resultType="user">
        select *
        from user
        where name = #{name}
    </select>
</mapper>
```

##### service层：

```java
public interface UserService {
    User login(String name);
}


@Service
public class UserServiceImpl implements UserService{

    @Autowired
    UserMapper userMapper;

    @Override
    public User login(String name) {
        return userMapper.login(name);
    }
}
```

##### 自定义Realm类：

```java
@Component
public class MyRealm extends AuthorizingRealm {
    @Autowired
    private UserService userService;

    /**
     * 自定义授权
     *
     * @param principalCollection 权限
     * @return {@link AuthorizationInfo}
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    /**
     * 自定义身份验证
     *
     * @param authenticationToken 令牌
     * @return {@link AuthenticationInfo}
     * @throws AuthenticationException 身份验证异常
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String name = authenticationToken.getPrincipal().toString();
        User user = userService.login(name);
        // 用户身份信息(用户名)，凭证信息(数据库里的密文密码)，ByteSource类型的盐，真实名称(用户名)
        if(user != null){
            return new SimpleAuthenticationInfo(authenticationToken.getPrincipal(),
                                                user.getPassword(), ByteSource.Util.bytes("salt"),name);
        }
        return null;
    }
}
```

##### shiro配置类

```java
@Slf4j
@Configuration
public class ShiroConfig {
    @Autowired
    private MyRealm myRealm;

    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager(){
        //1.创建DefaultWebSecurityManager对象
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        //2.创建加密对象，设置相关属性(使用什么方式加密，迭代次数)
        // 2.1设置加密方式为md5
        HashedCredentialsMatcher matcher = new HashedCredentialsMatcher("md5");
        // 2.2设置迭代加密次数为3
        matcher.setHashIterations(3);
        //3.将加密对象的配置传给myRealm
        myRealm.setCredentialsMatcher(matcher);
        //4.将myRealm注入到efaultWebSecurityManager对象中
        manager.setRealm(myRealm);
        log.info("DefaultWebSecurityManager 初始化成功");
        return manager;
    }

    @Bean
    public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
        DefaultShiroFilterChainDefinition defaultShiroFilterChainDefinition = new DefaultShiroFilterChainDefinition();
        // 无需认证
        defaultShiroFilterChainDefinition.addPathDefinition("/login","anon");
        // 需要认证
        defaultShiroFilterChainDefinition.addPathDefinition("/**","authc");
        return defaultShiroFilterChainDefinition;
    }
}
```

##### controller层

```java
@RestController
public class UserController {

    @GetMapping("/login")
    public String login(String name, String password) {
        //1.获取Subject对象
        Subject subject = SecurityUtils.getSubject();
        //2.封装请求数据到token
        UsernamePasswordToken token = new UsernamePasswordToken(name, password);
        //3.使用subject对象的login方法进行登陆验证
        try {
            subject.login(token);
            return "登陆成功";
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户不存在");
            return "用户不存在";
        } catch (IncorrectCredentialsException e) {
            e.printStackTrace();
            System.out.println("密码错误");
            return "密码错误";
        }
    }

}
```

##### 测试

我们启动项目后，在浏览器输入localhost:8080/login?name=Hakurei&password=Reimu后在页面得到登陆成功字样，同时密码错误和用户不存在也能生效，说明我们在springboot中配置shiro成功

#### 联系前端

login.html

```html
<body>
<h1>Shiro 登录认证</h1>
<br>
<form action="/userLogin">
    <div>用户名：<input type="text" name="name" value=""></div>
    <div>密码：<input type="password" name="password" value=""></div>
    <div><input type="submit" value="登录"></div>
</form>
</body>
```

main.html

```html
<body>
<h1>
    <br>
    Shiro 登录认证后主页面</h1>
登录用户为： <span th:text="${session.user}"></span>
</body>
```

UserController

```java
@Controller
public class UserController {

    @RequestMapping("/login")
    public String toLogin(){
        return "login";
    }

    @GetMapping("/userLogin")
    public String login(String name, String password, HttpSession session) {
        //1.获取Subject对象
        Subject subject = SecurityUtils.getSubject();
        //2.封装请求数据到token
        UsernamePasswordToken token = new UsernamePasswordToken(name, password);
        //3.使用subject对象的login方法进行登陆验证
        // 登陆成功进入main页面，登陆失败返回login页面
        try {
            subject.login(token);
            session.setAttribute("user",token.getPrincipal().toString());
            return "main";
            //return "登陆成功";
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户不存在");
            return "login";
        } catch (IncorrectCredentialsException e) {
            e.printStackTrace();
            System.out.println("密码错误");
            return "login";
        }
    }

}
```

### 多个realm的认证策略设置

#### 实现原理

当应用程序配置多个 Realm 时，例如：用户名密码校验、手机号验证码校验等等。 Shiro 的 ModularRealmAuthenticator 会使用内部的AuthenticationStrategy 组件判断认证是成功还是失败。
AuthenticationStrategy 是一个无状态的组件，它在身份验证尝试中被询问 4 次（这 4 次交互所需的任何必要的状态将被作为方法参数）：

1. 在所有 Realm 被调用之前
2. 在调用 Realm 的 getAuthenticationInfo 方法之前
3. 在调用 Realm 的 getAuthenticationInfo 方法之后
4. 在所有 Realm 被调用之后

认证策略的另外一项工作就是聚合所有 Realm 的结果信息封装至一个AuthenticationInfo 实例中，并将此信息返回，以此作为 Subject 的身份信息。

Shiro 中定义了 3 种认证策略的实现：

| **AuthenticationStrategy class** | **描述**                                                     |
| -------------------------------- | ------------------------------------------------------------ |
| AtLeastOneSuccessfulStrategy     | 只要有一个（或更多）的 Realm 验证成功，那么认证将视为成功    |
| FirstSuccessfulStrategy          | 第一个 Realm 验证成功，整体认证将视为成功，且后续 Realm 将被忽略 |
| AllSuccessfulStrategy            | 所有 Realm 成功，认证才视为成功                              |

`ModularRealmAuthenticator` 内置的认证策略默认实现是 `AtLeastOneSuccessfulStrategy` 方式。可以通过配置修改策略

#### 示例

```java
@Bean
public DefaultWebSecurityManager defaultWebSecurityManager(){ 
	//1 创建  defaultWebSecurityManager 对象
	DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
	//2 创建认证对象，并设置认证策略
	ModularRealmAuthenticator modularRealmAuthenticator = new ModularRealmAuthenticator();
	modularRealmAuthenticator.setAuthenticationStrategy(new AllSuccessfulStrategy());
	defaultWebSecurityManager.setAuthenticator(modularRealmAuthenticator) 
;
	//3 封装  myRealm 集合
    List<Realm> list = new ArrayList<>(); 
	list.add(myRealm);
	list.add(myRealm2);
	//4 将 myRealm 存入 defaultWebSecurityManager 对象
	defaultWebSecurityManager.setRealms(list);
	//5 返回
	return defaultWebSecurityManager;
}
```

### remember me的实现

Shiro 提供了记住我（RememberMe）的功能，比如访问一些网站时，关闭了浏览器， 下次再打开时还是能记住你是谁， 下次访问时无需再登录即可访问。

**基本流程**

- 首先在登录页面选中 RememberMe 然后登录成功；如果是浏览器登录，一般会 把 RememberMe 的 Cookie 写到客户端并保存下来；
- 关闭浏览器再重新打开；会发现浏览器还是记住你的；
- 访问一般的网页服务器端，仍然知道你是谁，且能正常访问；
- 但是，如果我们访问电商平台时，如果要查看我的订单或进行支付时，此时还是需要再进行身份认证的，以确保当前用户还是你。

#### 实现

修改配置文件

```java
@Slf4j
@Configuration
public class ShiroConfig {
    @Autowired
    private MyRealm myRealm;

    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager() {
        //1.创建DefaultWebSecurityManager对象
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        //2.创建加密对象，设置相关属性(使用什么方式加密，迭代次数)
        // 2.1设置加密方式为md5
        HashedCredentialsMatcher matcher = new HashedCredentialsMatcher("md5");
        // 2.2设置迭代加密次数为3
        matcher.setHashIterations(3);
        //3.将加密对象的配置传给myRealm
        myRealm.setCredentialsMatcher(matcher);
        //4.将myRealm注入到efaultWebSecurityManager对象中
        manager.setRealm(myRealm);
        //4.5 设置  rememberMe
        manager.setRememberMeManager(rememberMeManager());
        log.info("DefaultWebSecurityManager 初始化成功");
        return manager;
    }

    //cookie 属性设置
    public SimpleCookie rememberMeCookie() {
        SimpleCookie cookie = new SimpleCookie("rememberMe");
        //设置跨域
        //cookie.setDomain(domain);
        cookie.setPath("/");
        cookie.setHttpOnly(true);
        cookie.setMaxAge(30 * 24 * 60 * 60);
        return cookie;
    }

    //创建 Shiro 的  cookie 管理对象
    public CookieRememberMeManager rememberMeManager() {
        CookieRememberMeManager cookieRememberMeManager = new
                CookieRememberMeManager();
        cookieRememberMeManager.setCookie(rememberMeCookie());
        cookieRememberMeManager.setCipherKey("1234567890987654".getBytes());
        return cookieRememberMeManager;
    }

    @Bean
    public DefaultShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition defaultShiroFilterChainDefinition = new DefaultShiroFilterChainDefinition();
        // 无需认证
        defaultShiroFilterChainDefinition.addPathDefinition("/login", "anon");
        defaultShiroFilterChainDefinition.addPathDefinition("/userLogin", "anon");
        // 需要认证
        defaultShiroFilterChainDefinition.addPathDefinition("/**", "authc");
        //添加存在用户的过滤器（rememberMe）,也就是如果用户存在，那么直接进入，不在拦截
        defaultShiroFilterChainDefinition.addPathDefinition("/**", "user");
        return defaultShiroFilterChainDefinition;
    }
}
```

修改controller

```java
@Controller
public class UserController {

    @RequestMapping("/login")
    public String toLogin() {
        return "login";
    }

    @GetMapping("/userLogin")
    //public String login(String name, String password, HttpSession session)
    public String login(String name, String password, @RequestParam(defaultValue = "false") boolean rememberMe, HttpSession session) {
        //1.获取Subject对象
        Subject subject = SecurityUtils.getSubject();
        //2.封装请求数据到token
        // UsernamePasswordToken token = new UsernamePasswordToken(name, password);
        UsernamePasswordToken token = new UsernamePasswordToken(name, password, rememberMe);
        //3.使用subject对象的login方法进行登陆验证
        // 登陆成功进入main页面，登陆失败返回login页面
        try {
            subject.login(token);
            session.setAttribute("user", token.getPrincipal().toString());
            return "main";
            //return "登陆成功";
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户不存在");
            return "login";
        } catch (IncorrectCredentialsException e) {
            e.printStackTrace();
            System.out.println("密码错误");
            return "login";
        }
    }

    //登录认证验证  rememberMe
    @GetMapping("userLoginRm")
    public String userLogin(HttpSession session) {
        session.setAttribute("user", "hanser");
        return "main";
    }
}
```

在login页面加上remember选项

```html
<div>记住用户：<input type="checkbox" name="rememberMe" value="true"></div>
```

#### 测试

我们发现在不勾选记住我选项时，只要关闭了浏览器，那么就无法进入`http://localhost:8080/userLoginRm`页面，会被重定向回`/login`页面，而如果我们勾选了选项，那么只要session不过期，那么我们都能直接进入`http://localhost:8080/userLoginRm`页面

### 注销操作

用户登录后，配套的有登出操作。直接通过Shiro过滤器即可实现登出

在ShiroConfig的过滤器配置中加入

```java
//设置登出过滤器
defaultShiroFilterChainDefinition.addPathDefinition("/logout", "logout");
```

然后在想要设置注销操作的界面加上超链接,点击注销，就会自动跳转到登录界面，并且自动清除session中存的

```html
<a href="/logout">注销</a>
```

### 授权、角色认证

用户登录后，需要验证是否具有指定角色指定权限。Shiro也提供了方便的工具进判断

这个工具就是Realm的doGetAuthorizationInfo方法进行判断。触发权限判断的有两种:

1. 在页面中通过shiro:属性判断
2. 在接口服务中通过注解@Requiresxxx进行判断

#### 后端接口服务注解

通过给接口服务方法添加注解可以实现权限校验，可以加在控制器方法上，也可以加在业务方法上，一般加在控制器方法上。常用注解如下：

- `@RequiresAuthentication`
  验证用户是否登录，等同于方法subject.isAuthenticated()

- `@RequiresUser`
  验证用户是否被记忆：
  登录认证成功subject.isAuthenticated()为true
  登录后被记忆subject.isRemembered()为true

- `@RequiresGuest`
  验证是否是一个guest的请求，是否是游客的请求
  此时subject.getPrincipal()为null

- @RequiresRoles
  验证subject是否有相应角色，有角色访问方法，没有则会抛出异常
  AuthorizationException。
  例如：@RequiresRoles(“aRoleName”)
  void someMethod();
  只有subject有aRoleName角色才能访问方法someMethod()

- @RequiresPermissions
  验证subject是否有相应权限，有权限访问方法，没有则会抛出异常
  AuthorizationException。
  例如：

  @RequiresPermissions (“file:read”,”wite:aFile.txt”)
  void someMethod();
  subject必须同时含有file:read和wite:aFile.txt权限才能访问方someMethod()

#### 授权验证-获取角色进行验证

我们需要重写的是之前自定义Realm类`MyRealm`里的第一个方法`doGetAuthorizationInfo`

```java
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    System.out.println("进入自定义授权方法");
    //1 创建SimpleAuthorizationInfo对象，存储当前登录的用户的权限和角色
    SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
    //2 存储当前用户的角色信息
    info.addRole("admin");
    //返回信息
    return info;
}
```

在controller类中加入方法

```java
//验证角色
@RequiresRoles("admin")
@GetMapping("userLoginRole")
@ResponseBody
public String testRole(){
    System.out.println("该用户有admin角色");
    return "验证admin角色成功";
}
```

在main.html中加入验证用的a标签

```html
<a href="/userLoginRole">验证admin权限</a>
```

测试发现控制台和页面都成功输出，证明了权限判断会进入`doGetAuthorizationInfo`方法

我们的身份信息肯定是存储在数据库中的，那么接下来连接数据库试一试

建好对应的数据库表：

![image-20221207221502161](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202212072215225.png)

编写sql语句

```xml
<select id="getRole" resultType="string">
    select r.name
    from role r
             left join user u on r.id = u.rid
    where u.name = #{name}
</select>
```

之后在service层加入对应方法，并修改`doGetAuthorizationInfo`方法

```java
/**
 * 自定义授权
 *
 * @param principalCollection 权限
 * @return {@link AuthorizationInfo}
 */
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    System.out.println("进入自定义授权方法");
    //1 创建SimpleAuthorizationInfo对象，存储当前登录的用户的权限和角色
    SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
    //2 存储当前用户的角色信息
    info.addRole(userService.getRole(principalCollection.getPrimaryPrincipal().toString()));
    //返回信息
    return info;
}
```

再次测试，发现无误

#### 授权验证-获取权限进行验证

跟上面差不多,下面附上`doGetAuthorizationInfo`的重写，剩下的自行补全即可

```java
//自定义授权方法：获取当前登录用户权限信息，返回给 Shiro 用来进行授权对比
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) { 
	System.out.println("进入自定义授权方法");
	//获取当前用户身份信息
	String principal = principalCollection.getPrimaryPrincipal().toString();
    //调用接口方法获取用户的角色信息
	String role = userService.getRole(principal); 
	System.out.println("当前用户角色信息："+role);
	//调用接口方法获取用户角色的权限信息
	List<String> permissions = userService.getPermission(role);
	System.out.println("当前用户权限信息："+permissions); 
	//创建对象，存储当前登录的用户的权限和角色
	SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(); 
	//存储角色
	info.addRoles(roles);
	//存储权限信息
	info.addStringPermissions(permissions);
    //返回 
	return info;
}

```

### 异常处理

我们并不希望系统自带的异常直接显示在前端界面，因为这样用户体验很差，而且也并不安全，

所以我们可以自定义异常界面，只需要在controller目录下新建一个PermissionsException类，并加上@ControllerAdvice和@ExceptionHandler注解实现特殊异常处理：

```java
@ControllerAdvice
public class PermissionsException {
    @ResponseBody
    @ExceptionHandler(UnauthorizedException.class)
    public String unauthorizedException(Exception e) {
        return "无权限";
    }

    @ResponseBody
    @ExceptionHandler(AuthorizationException.class)
    public String authorizationException(Exception e) {
        return "权限认证失败";
    }
}
```

这样如果没有权限，会直接输出无权限；如果权限认证失败，会直接输出权限认证失败

### 前端页面授权验证

导入依赖：

```xml
<dependency>
<groupId>com.github.theborakompanioni</groupId> 
<artifactId>thymeleaf-extras-shiro</artifactId> 
<version>2.0.0</version>
</dependency>
```

ShiroConfig配置类中新加一个方法

```java
@Bean
public ShiroDialect shiroDialect(){
	return new ShiroDialect(); 
}
```

#### **Thymeleaf 中常用的 shiro:属性**

1. guest 标签 

   ```xml
   <shiro:guest>
   </shiro:guest>
   ```

   用户没有身份验证时显示相应信息，即游客访问信息。

2. user 标签

   ```xml
   <shiro:user> 
   </shiro:user>
   ```

   用户已经身份验证/记住我登录后显示相应的信息。

3. authenticated 标签

   ```xml
   <shiro:authenticated> 
   </shiro:authenticated>
   ```

   用户已经身份验证通过，即 Subject.login 登录成功，不是记住我登录的。

4. notAuthenticated 标签

   ```xml
   <shiro:notAuthenticated> 
   </shiro:notAuthenticated>
   ```

   用户已经身份验证通过，即没有调用 Subject.login 进行登录，包括记住我自动登录的也属于未进行身份验证。

5. principal 标签

   ```xml
   <shiro: principal/>
   <shiro:principal property="username"/>
   ```

   相当于((User)Subject.getPrincipals()).getUsername()。

6. lacksPermission 标签

   ```xml
   <shiro:lacksPermission name="org:create"> </shiro:lacksPermission>
   ```

   如果当前 Subject 没有权限将显示 body 体内容。

7. hasRole 标签

   ```xml
   <shiro:hasRole name="admin"> 
   </shiro:hasRole>
   ```

   如果当前 Subject 有角色将显示 body 体内容。

8. hasAnyRoles 标签

   ```xml
   <shiro:hasAnyRoles name="admin,user"> 
   </shiro:hasAnyRoles>
   ```

   如果当前 Subject 有任意一个角色（或的关系）将显示 body 体内容。

9. lacksRole 标签

   ```XML
   <shiro:lacksRole name="abc"> 
   </shiro:lacksRole>
   ```

   如果当前 Subject 没有角色将显示 body 体内容。

10. hasPermission 标签

    ```xml
    <shiro:hasPermission name="user:create"> 
    </shiro:hasPermission>
    ```

    如果当前 Subject 有权限将显示 body 体内容
