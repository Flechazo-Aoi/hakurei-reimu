---
title: spring(八)：proxy
categories: 前后端框架、中间件及工具学习
tags:
- spring
- 框架
---

**代理模式，springAOP的底层原理**，代理模式又分为静态代理和动态代理

## 静态代理

### 角色分析:

- 抽象角色: 一般会用接口或抽象类来实现(租赁房子这个业务)
```java
//接口中有一个方法，租房子的行为，房东和中介都要去实现它
public interface Rent {
    public void rent();
}
```
- 真实角色: 被代理的角色(房东)
```java
//房东(真实角色)，只关注真实业务；租房子，而不用去关注一些公共的业务:比如看房子，签合同
public class Host implements Rent{
    @Override
    public void rent() {
        System.out.println("房东出租房子");
    }
}
```
- 代理角色: 代理真实角色的角色(中介) 代理真实角色后，我们一般会做一些附属操作
```java
//中介(代理角色)，用来实现一些公共的业务，比如看房子，签合同，实现了业务的分工
//且公共业务发生拓展时，方便集中管理
public class Proxy implements Rent{
    private Host host;
    
    public Proxy(Host host) {
        this.host = host;
    }

    public Proxy() {
    }

    public void look(){
        System.out.println("客户看房子");
    }

    public void pay(){
        System.out.println("客户付中介费");
    }

    public void sign(){
        System.out.println("客户签合同");
    }

    public void rent(){
        sign();
        host.rent();
        pay();
    }
}

```
- 客户: 访问代理角色的角色
```java
//客户,只负责租赁房子
public class User {
    public static void main(String[] args) {
        Host host = new Host();
        Proxy proxy = new Proxy(host);
        proxy.look();
        proxy.rent();
    }
}

```
**优点**

- 可以使真实角色的操作更加纯粹(房东就是出租房子)，不用去关注一些公共业务
- 公共的业务交给代理角色实现，实现了业务的分工
- 且公共业务发生拓展时，方便集中管理

**缺点**

- 一个真实角色就会产生一个代理角色，会使代码量翻倍

## AOP的进一步理解

AOP:面向切面编程,从一个切面出发,实现业务的拓展,而不是直接修改源码
![img_1](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202304141014740.png)

## 动态代理

拥有的角色和静态代理一样，区别就是，动态代理的代理角色是通过一个动态代理类动态生成的，而不是像静态代理那样写死的

**动态代理分为两大类:基于接口的动态代理，基于类的动态代理**

- 基于接口:JDK动态代理(我们当前使用)
- 基于类: cglib
- java字节码实现: javasist(现在常用)

### 如何动态生成代理角色：

用到了两个类InvocationHandler、Proxy

- InvocationHandler:调用处理程序，反射包下的一个接口；
每个代理类(中介这种类)的实例都有一个关联的调用处理程序，每当代理实例调用代理对象(哪个接口)的方法时，
方法调用将被编码并分配到其调用处理程序的invoke()方法
- Proxy:代理类
提供的创建动态代理实例的静态方法

### 动态代理的实现

编写一个类

```java
package com.hanser.demo03;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//等会我们会用这个类自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {

    //被代理的接口target
    private Object target;

    public void setRent(Object target) {
        this.target = target;
    }

    //生成代理类实例
    // 用.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)方法
    //注意代理的类是一个接口，也就是中介代理rent这个业务，而不是代理房东
    //loader:类的加载路径，interfaces:要实现的接口,可以有多个,getInterfaces()得到对象的接口，用数组存储，h:调用处理程序
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(),this);
    }

    //处理代理实例，并返回结果,这个方法是代理实例调用被代理对象的方法时自动执行的，参数都是通过反射传入的
    //proxy就是生成的代理类实例，method就是代理类实例调用被代理对象的方法名，args就是方法的参数
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //动态代理的本质就是通过反射机制实现
        Object result = method.invoke(target, args);
        return result;
    }
}

```

之后就可以在用户那动态的创建代理实例了
```java
package com.hanser.demo03;

public class Client {

    public static void main(String[] args) {
        //真实角色
        Host host = new Host();

        //调用处理程序，需要ProxyInvocationHandler.java生成
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        //设置要代理的对象,这个对象是那个抽象接口的实现类(真实角色),
        //看上去是代理了房东,实际上底层是代理了那个接口
        pih.setRent(host);
        //生成动态代理类实例
        Rent proxy = (Rent) pih.getProxy();
        //利用反射执行方法，
        //代理类实例执行被代理对象的方法时，会利用反射自动获取方法名，和参数，并自动执行invoke方法来得到方法对应返回类型的结果
        proxy.rent();
    }
}

```

动态代理有静态代理的所有优点,而且还解决了静态代理代码量翻倍的问题

