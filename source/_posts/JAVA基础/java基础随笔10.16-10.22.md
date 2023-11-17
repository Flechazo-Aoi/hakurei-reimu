---
title: java基础随笔10.16-10.22
categories: java基础
date: 2023-10-22  15:22:33
tags: 
- java
- 随笔
---

## List<String> 转化String类型并去除[]

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("1");
    list.add("2");
    list.add("3");
    String s = list.toString();   //把List<String>转成String
    System.out.println(s);
    String replace = s.replace("[", "").replace("]", "");  //去掉转成String之后的[]
    System.out.println(replace);
    
    // 第二种方法：使用hutool的工具方法strip，可以去除字符串的前缀或者后缀
    String strip = StrUtils.strip(s, "[");
    String strip = StrUtils.strip(s, "]");
    System.out.println(strip);
}
```

## 将两个List合并

现有两个List：listA和ListB，将其合并成一个List

### Plain   JAVA

```java
listA.addAll(listB);
```

### JAVA8

```java
Stream.of(listA,listB).flatMap(x->x.stream()).collect(Collectors.toList());

List<T> list = Lists.newArrayList();
Stream.of(listA,listB).forEach(list:addAll);

Stream.concat(listA.stream(),list2.stream()).collect(Collectors.toList());
```

## 将数字转化为固定长度的字符串，不足补零

```java
String.format("%04d", 22); // 结果为"0022"
```

- 0代表前面要补的字符 
- 4代表字符串长度
- `d`表示参数为整数类型

##  instanceof

```
instanceof 是 Java 的保留关键字。它的作用是测试它左边的对象是否是它右边的类的实例，返回 boolean 的数据类型。
```

instanceof是Java中的二元运算符，左边是对象，右边是类；当对象是右边类或子类所创建对象时，返回true；否则，返回false。

**注意：**

- 类的实例包含本身的实例，以及所有直接或间接子类的实例
- `instanceof`左边显式声明的类型与右边操作元必须是同种类或存在继承关系，也就是说需要位于同一个继承树，否则会编译错误
- 左边的对象实例不能是基础数据类型(int、double等等)
- 因为null可以转换成为任何类型，所以不属于任何类型，`instanceof`结果会是false，且null只能放在`instanceof`的左边

简单理解，A isntanceof B：以左边类名为准，判断左右是否是存在父子关系，如果是则编译通过，否则编译报错！编译通过后，再判断A是否是B的实例对象或者B子类的对象！

### 应用场景

一般用于对象类型强制转换，在强转之前用它来判断是否可以强制转换：

```java
public B convert(A a) {
    if (a instanceof B) {
        return (B) a;
    }
    return null;
}
```





