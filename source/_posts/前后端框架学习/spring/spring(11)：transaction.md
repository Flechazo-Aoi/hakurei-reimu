---
title: spring(十一)：transaction
categories: 前后端框架学习
data: 2022-12-10  15:22:33
tags: 
- spring
- 框架 
---

Spring在不同的事务管理API之上定义了一个抽象层，使得开发人员不必了解底层的事务管理API就可以使用Spring的事务管理机制。Spring支持编程式事务管理和声明式的事务管理。

**编程式事务：**

- 将事务管理代码嵌到业务方法中来控制事务的提交和回滚(就是在Impl中加入try/catch)
- 缺点：必须在每个事务操作业务逻辑中包含额外的事务管理代码,会导致代码量变大

**声明式事务：AOP织入实现(推荐)**

- 一般情况下比编程式事务好用。
- 将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理;
- 将事务管理作为横切关注点，通过aop方法模块化。Spring中通过Spring AOP框架支持声明式事务管理。

## 声明式事务

首先要引入约束：

```
xmlns:tx="http://www.springframework.org/schema/tx"
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd
```
然后就可以用spring配置声明式事务,先要注册transactionManager类到spring中。注意transactionManager需要注入一个数据源，可以构造器注入,也可以set注入
```xml
<!--配置声明式事务-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="datasource"/>
</bean>
```
之后就要结合AOP实现事务的织入
- 配置事务通知,除了id之外,还需配transaction-manager事务管理,也就是上面配的
```xml
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--propagation,扩散，可以配置扩散类的事务属性，
    默认为REQUIRED用于增删改,SUPPORTS适用于查询，REQUIRES_NEW用于日志-->
    <!--给那些方法配置事务,name代表方法名，后面的表示要给这个方法配置的属性-->
    <tx:attributes>
        <!--方法名支持正则表达式，比如add**就代表以add开头的方法；*代表所有方法-->
        <tx:method name="addUser" propagation="REQUIRED"/>
        <tx:method name="deleteUser" propagation="REQUIRED"/>
        <tx:method name="selectUserById" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```
- 配置事务织入
```xml
    <!--配置事务织入-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.hanser.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
```
完成上述步骤之后，设置的方法就能由spring自动实现事务功能

## 思考

为什么需要事务？

- 如果不配置事务，可能存在数据提交不一致的情况；
- 如果我们不在Spring中去配置声明式事务，我们就需要在代码中手动配置事务！
- 事务在项目的开发中十分重要，涉及到数据的一致性和完整性问题，不容马虎！