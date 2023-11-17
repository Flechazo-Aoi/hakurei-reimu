---
title: Optional
categories: java基础
date: 2023-11-02  15:22:33
tags: 
- Optional
- java
---

## 简介

从 Java 8 引入的一个很有趣的特性是 Optional 类。Optional 类主要解决的问题是臭名昭著的空指针异常（`NullPointerException`） —— 每个 Java 程序员都非常了解的异常。

本质上，这是一个包含有可选值的包装类，这意味着 Optional 类既可以含有对象也可以为空。
Optional 是 Java 实现函数式编程的强劲一步，并且帮助在范式中实现。但是 Optional 的意义显然不止于此。
我们从一个简单的用例开始。在 Java 8 之前，任何访问对象方法或属性的调用都可能导致 NullPointerException：

```java
String isocode = user.getAddress().getCountry().getIsocode().toUpperCase();
```

在上述示例中，如果我们需要确保不触发异常，就得在访问每一个值之前对其进行明确地检查：

```java
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        Country country = address.getCountry();
        if (country != null) {
            String isocode = country.getIsocode();
            if (isocode != null) {
                isocode = isocode.toUpperCase();
            }
        }
    }
}
```

这很容易就变得冗长，难以维护。为了简化这个过程，我们来看看用 Optional 类是怎么做的。

## 使用Optional的优点

Optional 是一个对象容器，具有以下两个特点：

- 提示用户要注意该对象有可能为null
- 简化if else代码

## Optional使用

### 创建：

1. `Optional.empty()`： 创建一个空的 Optional 实例

```java
//返回一个Null的optional
Optional<String> empty = Optional.empty();
```

2. `Optional.of(T t)`： 创建一个 Optional 实例，当 t为null时抛出异常

```java
//of 方法的值不能为空否则会抛出异常
Optional<String> optional1 = Optional.of("hello");
```

3. `Optional.ofNullable(T t)`： 创建一个 Optional 实例，但当 t为null时不会抛出异常，而是返回一个空的实例

```java
//ofNullable 传入null不会异常
String str = null;
Optional<String> optional2 = Optional.ofNullable(str); 
```

### 获取：

`get()`：获取optional实例中的对象，当optional容器为空时会报错

### 判断：

- `isPresent()`：判断optional是否为空，如果空则返回false，否则返回true
- `orElse(T other)` :  如果optional不为空，则返回optional中的对象；如果为null，则返回 other 这个默认值
- `orElseGet(Supplier other)` :  如果optional不为空，则返回optional中的对象；如果为null，则使用生产者函数生成默认值other
- `orElseThrow(Supplier exception)` : 如果optional不为空，则返回optional中的对象；如果为null，则抛出生产者函数生成的异常

### 过滤：

`filter(Predicate p)`： 如果`optional`不为空，则执行函数p，如果p的结果为true，则返回原本的optional，否则返回空的optional

### 映射：

- `map(Function<T, U> mapper)`： 如果optional不为空，则将optional中的对象 t 映射成另外一个对象 u，并将 u 存放到一个新的optional容器中
- `flatMap(Function< T,Optional> mapper)`： 跟上面一样，在optional不为空的情况下，将对象t映射成另外一个optional

两个方法的区别在于：map方法会自动的将u放入optional中，而flatMap方法需要手动给u创建一个optional

## 具体案例

```java
public class OptionalTest {
    public static void main(String[] arg) {
        //1.创建Optional对象，如果参数为空直接抛出异常
        Optional<String> str=Optional.of("a");

        //2.获取Optional中的数据,如果不存在，则抛出异常
        System.out.println(str.get());      			// a

        //3.optional中是否存在数据
        System.out.println(str.isPresent());			// true

        //4.获取Optional中的值，如果值不存在，返回参数指定的值
        System.out.println(str.orElse("b"));			// a

        //5.获取Optional中的值，如果值不存在，返回lambda表达式的结果
        System.out.println(str.orElseGet(()->new Date().toString()));	// a

        //6.获取Optional中的值，如果值不存在，抛出指定的异常
        System.out.println(str.orElseThrow(()->new RuntimeException()));	// a



        Optional<String> str2=Optional.ofNullable(null);

        //7.optional中是否存在数据
        System.out.println(str2.isPresent());			// false

        //8.获取Optional中的值，如果值不存在，返回参数指定的值
        System.out.println(str2.orElse("b"));  			// b

        //9.获取Optional中的值，如果值不存在，返回lambda表达式的结果
        System.out.println(str2.orElseGet(()->new Date().toString()));	// 输出日期

        //10.获取Optional中的值，如果值不存在，抛出指定的异常
        System.out.println(str2.orElseThrow(()->new RuntimeException()));	// 抛出运行异常
    }
}
```

