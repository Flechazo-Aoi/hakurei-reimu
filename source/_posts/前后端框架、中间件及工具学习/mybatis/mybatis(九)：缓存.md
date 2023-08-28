---
title: mybatis(九)：缓存
categories: 前后端框架、中间件及工具学习
tags:
- mybatis
- 框架
---



首先,频繁的查询数据库,也就是进行IO操作,是很消耗资源的.所以我们就想：能不能将一次查询的结果暂存到一个可以直接读取的内存的一小块,需要用到时直接拿,就不用再去查询数据库了。这一小块就被称为缓存

> 我们知道：频繁的查询数据库,也就是进行IO操作,是很消耗资源的.
>
> 所以我们就想：能不能将一次查询的结果暂存到一个可以直接读取的内存的一小块,需要用到时直接拿,就不用再去查询数据库了。
>
> 这一小块就被称为缓存

## 什么是缓存

- 存在内存中的临时数据
- 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

为什么使用缓存？

- 减少和数据库的交互次数，减少系统开销，提高系统效率。

什么样的数据能使用缓存？

- 经常查询并且不经常改变的数据。

## Mybatis的缓存

MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大提升查询效率。

MyBatis系统中默认定义了两级缓存：一级缓存和二级缓存

- 默认情况下，只有一级缓存开启。(SqlSession级别的缓存，sqlSession关闭时,缓存清空,也称为本地缓存)
- 二级缓存需要手动开启和配置，他是基于命名空间namespace级别的缓存(不同命名空间的mapper查出的数据会放在自己对应的缓存中)。
- 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

### 一级缓存

一级缓存也叫本地缓存：SqlSession级别的

- 与数据库同一次会话(一个sqlSession从开启到关闭)期间查询到的数据会放在本地缓存中。
- 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

测试：

```java
    public class MyTest{
    //因为在一个会话中,所以user1查询之后会放在缓存中,第二次查询,user2会发现缓存中已经有了,直接从缓存中取,日志显示,只执行了一次sql语句
    public void test1(){
        SqlSession session = MybatisUtils.getSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user1 = mapper.getUserById(1);
        System.out.println(user1);

        System.out.println("==========================");

        User user2 = mapper.getUserById(1);
        System.out.println(user2);

        System.out.println(user1 == user2);
        session.close();
    }
}
```
#### 缓存失效情况

- 查询不同的东西(第一次查询,内存中没有对应缓存)
- 增删改操作，可能会改变原来的数据，所以会刷新缓存！
- 查询不同的Mapper.xml
- 缓存不会定时进行刷新,只有缓存满了,才会根据设置的算法来清除不需要的缓存
- 手动清理缓存！--> sqlSession.clearCache

### 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存.
- 基于namespace级别的缓存，一个命名空间，也就是一个Mapper,对应一个二级缓存；

#### 工作机制

- 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
- 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
- 新的会话查询信息，就可以从二级缓存中获取内容：
- 不同的mapper查出的数据会放在自己对应的缓存(map)中；

#### 二级缓存使用步骤

- 在核心配置文件下开启全局缓存

```xml
<!--显式的开启全局缓存-->
<setting name="cacheEnabled" value="true"/>
```
- 在需要使用二级缓存的Mapper中开启
```xml

<!--在当前命名空间的Mapper.xml中使用二级缓存,且自定义一些配置-->
<cache
        eviction="FIFO"
        flushInterval="60000"
        size="512"
        readOnly="true"/>
```
- 3.测试
```java
public class MyTest{
    //session1已经关闭了,它的一级缓存会放到二级缓存中,所以session2查询时,看到二级缓存中有对应的值,能直接取出来
    public void test2(){
        SqlSession session1 = MybatisUtils.getSession();
        SqlSession session2 = MybatisUtils.getSession();
        UserMapper mapper1 = session1.getMapper(UserMapper.class);
        UserMapper mapper2 = session2.getMapper(UserMapper.class);
        User user1 = mapper1.getUserById(1);
        System.out.println(user1);
        session1.close();

        System.out.println("==========================");
        User user2 = mapper2.getUserById(1);
        System.out.println(user1);
        System.out.println(user1 == user2);
        session2.close();
    }
}
```
`注意：如果要开启二级缓存,我们需要将实体类序列化,否则会报错`


## 小结

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会先放在一级缓存中；
- 只有当会话提交，或者关闭的时候，才会提交到二级缓冲中

缓存原理

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304132226848.png)