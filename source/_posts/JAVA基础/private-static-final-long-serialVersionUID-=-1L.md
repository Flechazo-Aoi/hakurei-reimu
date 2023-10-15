---
title: private static final long serialVersionUID = 1L
categories: java基础
date: 2023-10-10 17:22:33
tags: 
- java
- 随笔
---

这句话的意思是**定义程序序列化ID**

## 什么是序列化

- `Serializable`，Java的一个接口，用来完成java的序列化和反序列化操作的；
- 任何类型只要实现了`Serializable`接口，就可以被保存到文件中，或者作为数据流通过网络发送到别的地方。也可以用管道来传输到系统的其他程序中
- java**序列化**是指**把java对象转换为字节序列**的过程，而java**反序列化**是指把**字节序列恢复为java对象**的过程

## 序列化id (serialVersionUID)

- 序列化ID，相当于身份认证，主要用于程序的版本控制，保持不同版本的兼容性，在程序版本升级时避免程序报出版本不一致的错误。
- 如果定义了`private static final long serialVersionUID = 1L`，那么如果你忘记修改这个信息，而且你对这个类进行修改的话，这个类也能被进行反序列化，而且不会报错。一个简单的概括就是，如果你忘记修改，那么它是会版本向上兼容的。
- 如果没有定义一个名为`serialVersionUID`，类型为long的变量，Java序列化机制会进行隐式声明，即根据编译的class自动生成一个`serialVersionUID`。这种情况下，只有同一次编译生成的class才会生成相同的`serialVersionUID` 。此时如果对某个类进行修改的话，那么版本上面是不兼容的，就会出现反序列化报错的情况

在实际的开发中，重新编译会影响项目进度部署，所以我们为了提高开发效率，不希望通过编译来强制划分软件版本，就需要显式地定义一个名为`serialVersionUID`，类型为long的变量，不修改这个变量值的序列化实体都可以相互进行串行化和反串行化。