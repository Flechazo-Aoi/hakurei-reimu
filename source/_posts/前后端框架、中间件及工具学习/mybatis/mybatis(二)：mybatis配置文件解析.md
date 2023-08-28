---
title: mybatis(二)：mybatis配置文件解析
categories: 前后端框架、中间件及工具学习
tags:
- mybatis
- 框架 
---

## 核心配置文件mybatis-config.xml   

mybatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息，如下：

- configuration（配置）
- properties（属性）
- settings（设置）
- typeAliases（类型别名）
- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins（插件）
- environments（环境配置）
- environment（环境变量）
- transactionManager（事务管理器）
- dataSource（数据源）
- databaseIdProvider（数据库厂商标识）
- mappers（映射器）

## 环境配置(environments) 

mybatis可以配置多个环境,尽管可以配置多个环境，但每个SqlSessionFactory实例只能选择一种环境。

- 直接在配置文件设置属性值

```xml
<!--default="development"就代表默认的环境是"development"-->
<environments default="development">
    <!--每个环境有自己的id-->
    <environment id="development">
        <!--Mybatis默认的事务管理器就是JDBC,-->
        <transactionManager type="JDBC"/>
        <!--Mybatis默认连接池：POOLED.这种数据源的实现利用池的概念将JDBC连接对象组织起来
        ,避免了创建新的连接实例时所必须的初始化和认证时间.这是一种使得并发web应用快速响应请求的流行处理方式-->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?
                   useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
        </dataSource>
    </environment>
</environments>
```
- 引入配置文件
```xml
<!--另一个环境,可以直接用${}取文件里的值-->
<!--其中属性可以从外部引入，但一定要写到文件开头,xml可以规定标签的顺序-->
<properties resource="db.properties">
    <!--可以在这里设置值，不过因为在这里动态设置是第一个读取的，后面(读取配置文件或者在property中设置)的如果有同名的属性值，会覆盖掉这里的记录-->
    <property name="pwd" value="111111"/>
</properties>

<environments default="test">
    <environment id="test">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${pwd}"/>
        </dataSource>
    </environment>
</environments>
```
## 类型别名(typeAliases)

- 类型别名是为Java类型设置一个短的名字。
- 存在的意义仅在于用来减少类完全限定名的冗余。
```xml
<!--
    可以用typeAliases设置别名，存在的意义仅为减少类完全限定名的冗余
    第一种 <typeAlias type="com.hanser.pojo.User" alias="user"/>是直接给实体类起别名，以后使用直接用设置的alias即可
    第二种 <package name="com.hanser.pojo"/>是指定一个包名，mybatis会在包名下搜索需要的JavaBean
    比如扫描实体类的包，它的默认别名就是这个类的类名，但是首字母小写，也就是user
    在实体类比较少时，使用第一种，如果实体类比较多，建议使用第二种
    第一种可以自定义名字，第二种不可以，如果非要自定义，可以在实体上增加注解
    -->
    <typeAliases>
        <typeAlias type="com.hanser.pojo.User" alias="user"/>
    </typeAliases>
```
## 设置(settings)

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。

- logImpl : 指定mybatis所用日志的具体实现,未指定时将自动查找,可设置为STDOUT_LOGGING标准日志
- cacheEnable : 全局的开启或关闭配置文件中所有映射器已经配置的任何缓存,默认为true

## 映射器(mappers)

MapperRegistry:注册绑定我们的Mapper文件；

- 方式1

```xml
<!--直接绑定一个类路径-->
<mapper resource="com/hanser/dao/UserMapper.xml"/>
```
- 方式2
```xml
<!--使用映射接口实现类的完全限定类名绑定注册,绑定一个接口,需要接口和配置文件同名，且接口和配置文件在同一个包下
如果把Mapper.xml放入了resources目录下,那么要在该目录下建跟原Mapper接口一样的包,这样编译后才会整合到一个包下-->
<mapper class="com.hanser.dao.UserMapper"/>
```
- 方式3
```xml
<!--使用扫描包名进行注入绑定,将包内映射接口实现全部注册为映射器
需要接口和配置文件同名，且接口和配置文件在同一个包下-->
<package name="com.hanser.dao"/>
```
## 生命周期和作用域

生命周期是至关重要的，因为错误的使用会导致非常严重的并发问题。

### SqlSessionFactoryBuilder：

- 一但创建了SqlSessionFactory,他就不需要了
- 所以设置为局部变量,也就是类的一个局部变量

### SqlSessionFactory:

- 说白了就是可以想象为：数据库连接池
- SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
- 因此SqlSessionFactory的最佳作用域是应用作用域。
- 最好的实现就是单例模式或静态单例模式

### SqlSession:

- 他就是连接到连接池的一个请求
- 不是线程安全的,因此是不能共享的,所以最佳作用域是一个请求或方法作用域(请求时获取,响应时关闭)。
- 使用完要及时关闭,否则资源会被占用