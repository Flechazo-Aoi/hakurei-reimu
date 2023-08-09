---
title: java基础部分
categories: 计算机基础
tags: 
- 面试
- java基础
---

本文参考[马士兵java面试八股文](https://blog.csdn.net/weixin_57034203/article/details/125471520)，进行了梳理和分类，作为面试笔记留档

## 面向对象三大特性 

 面向对象编程是利用类和对象编程的一种思想。万物可归类，类是对于世界事物的高度抽象 ，是具有相同特征的实体的集合，不同的事物之间有不同的关系 ，一个类自身与外界的封装关系，一个父类和子类的继承关系， 一个类和多个类的多态关系。万物皆对象，对象是具体的世界事物，**面向对象的三大特征封装，继承，多态**。封装，封装说明一个类行为和属性与其他类的关系，低耦合，高内聚；继承是父类和子类的关系，多态说的是类与类的关系。

### 封装

**隐藏了类的内部实现机制**。可以在不影响使用的情况下改变类的内部结构，同时也保护了数据。对外界而已它的内部细节是隐藏的，暴露给外界的只是它的访问方法。**封装包括类的封装和属性的封装**。

- 属性的封装：使用者只能通过事先定制好的方法来访问数据，可以方便地加入逻辑控制，限制对属性的 不合理操作；
- 方法的封装：使用者按照既定的方式调用方法，不必关心方法的内部实现，便于使用；便于修改，增强代码的可维护性；

### 继承

**从已有的类中派生出新的类**，新的类能继承已有类的数据属性和行为，并能扩展新的能力。在本质上是特殊到一般的关系，即常说的is-a关系。子类继承父类，表明子类是一种特殊的父类，并且具有父类所不具有的一些属性或方法。从多种实现类中抽象出一个基类，使其具备多种实现类的共同特性 ，当实现类用extends关键字继承了基类（父类）后，实现类就具备了这些相同的属性。继承的类叫做子类（派生类或者超类），被继承的类叫做父类（或者基类）。比如从猫类、狗类、虎类中可以抽象出一个动物类，具有和猫、狗、虎类的共同特性（吃、跑、叫等）。Java通过extends关键字来实现继承，**父类中通过private定义的变量和方法不会被继承**，不能在子类中直接操作父类通过private定义的变量以及方法。继承避免了对一般类和特殊类之间共同特征进行的重复描述，通过继承可以清晰地表达每一项共同特征所适应的概念范围，在一般类中定义的属性和操作适应于这个类本身以及它以下的每一层特殊类的全部对象。运用继承原则使得系统模型比较简练也比较清晰。

### 多态

**封装和继承最后归结于多态**。多态指的是类和类的关系，两个类由继承关系，存在有方法的重写，故而可以在调用时有父类引用指向子类对象。多态必备三个要素：继承，重写，父类引用指向子类对象。

## HashMap原理，在jdk1.7和1.8中有什么区别

HashMap根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序确实不确定的，HashMap最多允许一条记录的键为null，允许多条记录的值为null，HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果要满足线程安全，可以用Collections的synchronizedMap方法使HashMap具有线程安全HashMap的结构

![image-20230309095310127](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303090953400.png)

HashMap 里面是一个数组，然后数组中每个元素是一个单向链表。上图中，每个绿色的实体是嵌套类 Entry 的实例，Entry 包含四个属性：key, value, hash 值和用于单向链表的 next。

- capacity：当前数组容量，始终保持 2^n，可以扩容，扩容后数组大小为当前的 2 倍。
- loadFactor：负载因子，默认为 0.75。
- threshold：扩容的阈值，等于 capacity * loadFactor，超过该值就会扩容

![image-20230309095439253](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303090954471.png)

Java8 对 HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以其由 数组+链表+红黑树 组成。

根据 Java7 HashMap 的介绍，我们知道，查找的时候，根据 hash 值我们能够快速定位到数组的具体下标，但是之后的话，需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决于链表的长度，为 O(n)。为了降低这部分的开销，在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)。

## ArrayList 和 LinkedList 的区别

- **数据结构实现**：ArrayList 是动态数组的数据结构实现，而 LinkedList 是双向链表的数据结构实现。
- **随机访问效率**：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是线性的数据存储方式，所以需要移动指针从前往后依次查找。
- **增加和删除效率**：在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为ArrayList 增删操作要影响数组内的其他数据的下标。
- **内存空间占用**：LinkedList 比 ArrayList 更占内存，因为 LinkedList 的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。
- **线程安全**：ArrayList 和 LinkedList 都是不同步的，也就是不保证线程安全；

综合来说，在需要频繁读取集合中的元素时，更推荐使用 ArrayList，而在插入和删除操作较多时，更推荐使用 LinkedList。

> 补充：数据结构基础之双向链表
> 双向链表也叫双链表，是链表的一种，它的每个数据结点中都有两个指针，分别指向直接后继和直接前驱。所以，从双向链表中的任意一个结点开始，都可以很方便地访问它的前驱结点和后继结点。

## 高并发中的集合有哪些问题

### 第一代线程安全集合类

Vector、Hashtable。

使用synchronized修饰方法保证线程安排。缺点：效率低下

### 第二代线程非安全集合类

ArrayList、HashMap。

线程不安全，但是性能好，用来替代Vector、Hashtable。

> 使用ArrayList、HashMap，需要线程安全怎么办呢？

使用` Collections.synchronizedList(list)`; `Collections.synchronizedMap(m)`;

底层使用synchronized代码块锁 虽然也是锁住了所有的代码，但是锁在方法里边，并所在方法外边性能可以理解为稍有提高吧。毕竟进方法本身就要分配资源的。

### 第三代线程安全集合类

在大量并发情况下如何提高集合的效率和安全呢？

java.util.concurrent.*

ConcurrentHashMap：

CopyOnWriteArrayList ：

CopyOnWriteArraySet： 注意 不是CopyOnWriteHashSet*

底层大都采用Lock锁（1.8的ConcurrentHashMap不使用Lock锁），保证安全的同时，性能也很高。

## jdk1.8的新特性

### 接口的默认方法

Java 8允许我们给接口添加一个非抽象的方法实现，只需要使用 default关键字即可，这个特征又叫做扩展方法，示例如下：

```java
public interface test {
    double calculate(int a);
    // extension method必须要有方法体
    default double sqrt(int a){
        return Math.sqrt(a);
    }
}
```

在我们实现接口之后，可以选择对方法直接使用或者重写。

### Lambda表达式

首先看看在老版本的Java中是如何排列字符串的：

```java
List list = Arrays.asList("Pachouli","Hakurei","Sakuya","Reimu");
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        // 从第一个元素开始做减法(char类型)，返回值是第一个不等的字符的ASIC差值，如果前缀都相等，就返回字符串长度的差值
        return o1.compareTo(o2);
    }
});
```

只需要给静态方法 Collections.sort 传入一个List对象以及一个比较器来按指定顺序排列。通常做法都是创建一个匿名的比较器对象然后将其传递给sort方法。

在Java 8 中你就没必要使用这种传统的匿名对象的方式了，Java 8提供了更简洁的语法，lambda表达式：

```java
Collections.sort(names, (String a, String b) -> { return b.compareTo(a); });
```

看到了吧，代码变得更段且更具有可读性，但是实际上还可以写得更短：

```java
Collections.sort(names, (String a, String b) -> b.compareTo(a));
```

对于函数体只有一行代码的，你可以去掉大括号{}以及return关键字，但是你还可以写得更短点：

```java
Collections.sort(names, (a, b) -> b.compareTo(a));
```

Java编译器可以自动推导出参数类型，所以你可以不用再写一次类型。

### 函数式接口

Lambda表达式是如何在java的类型系统中表示的呢？每一个lambda表达式都对应一个类型，通常是接口类型。而“函数式接口”是指仅仅只包含一个抽象方法的接口，每一个该类型的lambda表达式都会被匹配到这个抽象方法。因为默认方法不算抽象方法，所以你也可以给你的函数式接口添加默认方法。

我们可以将lambda表达式当作**任意只包含一个抽象方法的接口类型**，确保你的接口一定达到这个要求，你只需要给你的接口添加 `@FunctionalInterface `注解，编译器如果发现你标注了这个注解的接口有多于一个抽象方法的时候会报错。

示例：

```java
@FunctionalInterface
interface Converter<F, T> {  	// 将F类型的from转换成T类型的变量
    T convert(F from);
}
```

```java
// 调用函数式接口，这里表示把String类型的from转变成Integer类型
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted); // 123
```

需要注意如果@FunctionalInterface如果没有指定，上面的代码也是对的。

将lambda表达式映射到一个单方法的接口上，这种做法在Java 8之前就有别的语言实现，比如Rhino JavaScript解释器，如果一个函数参数接收一个单方法的接口而你传递的是一个function，Rhino 解释器会自动做一个单接口的实例到function的适配器，典型的应用场景有 org.w3c.dom.events.EventTarget 的addEventListener 第二个参数 EventListener。

### 方法与构造函数引用

前一节中的代码还可以通过静态方法引用来表示：

> **定义：** **在类中使用static修饰的静态方法会随着类的定义而被分配和装载入内存中；而非静态方法属于对象的具体实例，只有在类的对象创建时在对象的内存中才有这个方法的代码段。**
>
> **注意：** 非静态方法既可以访问静态数据成员 又可以访问非静态数据成员，而静态方法只能访问静态数据成员；
> 非静态方法既可以访问静态方法又可以访问非静态方法，而静态方法只能访问静态数据方法。
>
> **原因：** 因为静态方法和静态数据成员会随着类的定义而被分配和装载入内存中，而非静态方法和非静态数据成员只有在类的对象创建时在对象的内存中才有这个方法的代码段。
>
> 引用静态方法时，可以用类名.方法名或者对象名.方法名的形式。

```java
// 等价于 return Integer.valueOf(from)
Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted); // 123
```

Java 8 允许你使用`::`关键字来传递方法或者构造函数引用，上面例子是引用一个静态方法，我们也可以引用一个对象的方法。

除此以外，构造函数也可以使用`::`关键字来引用，首先我们定义一个包含多个构造函数的简单类：

```java
class Person {
    String firstName;
    String lastName;

    Person() {
    }

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

接下来我们指定一个用来创建Person对象的对象工厂接口：

```java
interface PersonFactory {
    P create(String firstName, String lastName);
}
```

这里我们使用构造函数引用来将他们关联起来，而不是实现一个完整的工厂：

```java
PersonFactory personFactory = Person::new;
Person person = personFactory.create("Hakurei", "Reimu");
```

我们只需要使用 Person::new 来获取Person类构造函数的引用，Java编译器会自动根据PersonFactory.create方法的签名来选择合适的构造函数。

### Lambda 作用域

在lambda表达式中访问外层作用域和老版本的匿名对象中的方式很相似。你可以直接访问标记了final的外层局部变量，或者实例的字段以及静态变量。

### 访问局部变量



## Java中的重写和重载

方法的重载和重写都是实现多态的方式，区别在于前者实现的是**编译时的多态性**，而后者实现的是**运行时的多态性**

重载发生在一个类中，同名的方法如果有不同的参数列表（参数类型不同、参数个数不同或者二者都不同）则视为重载；

重写发生在子类与父类之间，重写要求子类被重写方法与父类被重写方法有相同的返回类型，比父类被重写方法更好访问，不能比父类被重写方法声明更多的异常（里氏代换原则）。重载对返回类型没有特殊的要求。

方法重载的规则：

- 方法名一致，参数列表中参数的顺序，类型，个数不同。
- 重载与方法的返回值无关，存在于父类和子类，同类中。
- 可以抛出不同的异常，可以有不同修饰符

方法重写的规则：

- 参数列表必须完全与被重写方法的一致，返回类型必须完全与被重写方法的返回类型一致。
- 构造方法不能被重写，声明为 final 的方法不能被重写，声明为 static 的方法不能被重写，但是能够被再次声明。
- 访问权限不能比父类中被重写的方法的访问权限更低。
- 重写的方法能够抛出任何非强制异常（UncheckedException，也叫非运行时异常），无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。

![ssss](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091019068.png)

## 接口和抽象类有哪些区别

不同点：

- 抽象类中可以定义构造器，接口中不能定义构造器
- 抽象类可以有抽象方法和具体方法，接口中的方法全部都是抽象方法(1.8之前。抽象方法就是用public abstract修饰的方法，不过在接口中，这两个词可以省略)
- 抽象类中的成员可以是 private、默认、protected、public，接口中的成员全都是 public 的
- 抽象类中可以定义成员变量，接口中定义的成员变量实际上都是常量
- 抽象类中可以包含静态方法，接口中不能有静态方法
- 一个类只能继承一个抽象类，但可以实现多个接口。
- 有抽象方法的类必须被声明为抽象类，但而抽象类未必要有抽象方法.

相同点：

- 不能够实例化
- 都可以作为引用类型
- 一个类如果继承了某个抽象类或者实现了某个接口都需要对其中的抽象方法全部进行实现，否则该类仍然需要被声明为抽象类

## 怎样声明一个类不会被继承，什么场景下会用

如果一个类被final修饰，此类不可以有子类，也就是不能被其它类继承，如果一个类中的所有方法都没有重写的需要，也不需要被子类继承，就可以使用final修饰类。

## Java中==和equals有哪些区别

`equals`和`==`最大的区别是一个是方法一个是运算符。

**==：**如果比较的对象是基本数据类型，则比较的是数值是否相等；如果比较的是引用数据类型，则比较的是对象的地址值是否相等。

每新new一个引用类型的对象，会重新分配堆内存空间，使用==比较返回false。

**equals()：**Object类的一个方法，Java当中所有的类都是继承于Object这个超类。默认情况下，比较内存地址值是否相等。可以按照需求逻辑，重写对象的equals方法。

## String、StringBuffer、StringBuilder区别及使用场景

Java 平台提供了两种类型的字符串：String 和 StringBuffer/StringBuilder，它们都可以储存和操作字符串，区别如下：

**String 是只读字符串**，也就意味着 String 引用的字符串内容是不能被改变的。你可能会举反例说下面这个例子str就是可变的：

```java
String str = “abc”；
str = “bcd”;
```

但实际上String类型在java中是引用类型的，也就是例子中的str指向的是内存中'abc'这个字符串对象的地址，后续只是将str重新指向了一个新的字符串'bcd'，而字符串'abc'并没有发生改变。

**StringBuffer/StringBuilder 表示的字符串对象可以直接进行修改**。

StringBuilder 是 Java5 中引入的，它和 StringBuffer 的方法完全相同，区别在于它是在单线程环境下使用的，因为它的所有方法都没有被 synchronized 修饰，因此它的效率理论上也比 StringBuffer 要高。

> 那么为什么java中的String是不可变的？

参考[Java中的String为什么是不可变的？](https://blog.csdn.net/dearKundy/article/details/82355019)

## Java代理的几种实现方式

第一种:静态代理,只能静态的代理某些类或者某些方法,不推荐使用,功能比较弱,但是编码简单

第二种:动态代理,包含Proxy代理和CGLIB动态代理

### Proxy代理

Proxy代理是JDK内置的动态代理。

特点:面向接口的,不需要导入三方依赖的动态代理,可以对多个不同的接口进行增强,通过反射读取注解时,只能读取到接口上的注解

 原理:面向接口,只能对实现类在实现接口中定义的方法进行增强

定义接口和实现：

```java
package com.proxy;

public interface UserService {
    public String getName(int id);

    public Integer getAge(int id);
}
```

```java
package com.proxy;

public class UserServiceImpl implements UserService {
    @Override
    public String getName(int id) {
        System.out.println("------getName------");
        return "reimu";
    }

    @Override
    public Integer getAge(int id) {
        System.out.println("------getAge------");
        return 26;
    }
}
```

```java
package com.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class MyInvocationHandler implements InvocationHandler {

    public Object target;

    MyInvocationHandler() {
        super();
    }

    MyInvocationHandler(Object target) {
        super();
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if ("getName".equals(method.getName())) {
            System.out.println("++++++before " + method.getName() + "++++++");
            Object result = method.invoke(target, args);
            System.out.println("++++++after " + method.getName() + "++++++");
            return result; 
        } else {
            Object result = method.invoke(target, args);
            return result;
        }
    }
}
```

```java
package com.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Proxy;

public class Main1 {
    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        InvocationHandler invocationHandler = new MyInvocationHandler(userService);
        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(userService.getClass().getClassLoader(),
                userService.getClass().getInterfaces(),invocationHandler);
        System.out.println(userServiceProxy.getName(1));
        System.out.println(userServiceProxy.getAge(1));
    }
}
```

### CGLIB动态代理

 特点:面向父类的动态代理,需要导入第三方依赖

 原理:面向父类,底层通过子类继承父类并重写方法的形式实现增强

Proxy和CGLIB是非常重要的代理模式,是springAOP底层实现的主要两种方式

CGLIB的核心类：
net.sf.cglib.proxy.Enhancer – 主要的增强类
net.sf.cglib.proxy.MethodInterceptor – 主要的方法拦截类，它是Callback接口的子接口，需要用户实现
net.sf.cglib.proxy.MethodProxy – JDK的java.lang.reflect.Method类的代理类，可以方便的实现对源对象方法的调用,如使用：
Object o = methodProxy.invokeSuper(proxy, args);//虽然第一个参数是被代理对象，也不会出现死循环的问题。

net.sf.cglib.proxy.MethodInterceptor接口是最通用的回调（callback）类型，它经常被基于代理的AOP用来实现拦截（intercept）方法的调用。这个接口只定义了一个方法
public Object intercept(Object object, java.lang.reflect.Method method,
Object[] args, MethodProxy proxy) throws Throwable;

第一个参数是代理对像，第二和第三个参数分别是拦截的方法和方法的参数。原来的方法可能通过使用java.lang.reflect.Method对象的一般反射调用，或者使用 net.sf.cglib.proxy.MethodProxy对象调用。net.sf.cglib.proxy.MethodProxy通常被首选使用，因为它更快。

```java
package com.proxy.cglib;

import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import java.lang.reflect.Method;
 
public class CglibProxy implements MethodInterceptor {
    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("++++++before " + methodProxy.getSuperName() + "++++++");
        System.out.println(method.getName());
        Object o1 = methodProxy.invokeSuper(o, args);
        System.out.println("++++++before " + methodProxy.getSuperName() + "++++++");
        return o1;
    }
}
```

```java
package com.proxy.cglib;
 
import com.test3.service.UserService;
import com.test3.service.impl.UserServiceImpl;
import net.sf.cglib.proxy.Enhancer;
 
public class Main2 {
    public static void main(String[] args) {
        CglibProxy cglibProxy = new CglibProxy();
 
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(UserServiceImpl.class);
        enhancer.setCallback(cglibProxy);
 
        UserService o = (UserService)enhancer.create();
        o.getName(1);
        o.getAge(1);
    }
}
```

## hashcode和equals

equals()源自于java.lang.Object,该方法用来简单验证两个对象的相等性。Object类中定义的默认实现只检查两个对象的对象引用，以验证它们的相等性。 通过重写该方法,可以自定义验证对象相等新的规则,如果你使用ORM(对象关系映射，Object Relational Mapping，是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术。)处理一些对象的话，你要确保在hashCode()和equals()对象中使用getter和setter而不是直接引用成员变量。

hashCode()源自于java.lang.Object ,该方法用于获取给定对象的唯一的整数(散列码)。当这个对象需要存储在哈希表这样的数据结构时，这个整数用于确定桶的位置。默认情况下，对象的hashCode()方法返回对象所在内存地址的整数表示。hashCode()是HashTable、HashMap和HashSet使用的。默认的，Object类的hashCode()方法返回这个对象存储的内存地址的编号。

hash散列算法,使得在hash表中查找一个记录速度变O(1). 每个记录都有自己的hashcode,散列算法按照hashcode把记录放置在合适的位置. 在查找一个记录,首先先通过hashcode快速定位记录的位置.然后再通过equals来比较是否相等。如果hashcode没找到，则不equal，元素不存在于哈希表中；即使找到了，也只需执行hashcode相同的几个元素的equal，如果不equal，还是不存在哈希表中。

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091505726.png)

## HashMap和HashTable

### HashMap和HashTable对比

1. HashTable线程同步，HashMap非线程同步。
2. HashTable不允许<键,值>有空值，HashMap允许<键,值>有空值。
3. HashTable使用Enumeration，HashMap使用Iterator。
4. HashTable中hash数组的默认大小是11，增加方式的old*2+1，HashMap中hash数组的默认大小是16，增长方式是2的指数倍。
5. HashMap jdk1.8之前list + 链表 jdk1.8之后list + 链表,当链表长度到8时，转化为红黑树
6. HashMap链表插入节点的方式 在Java1.7中，插入链表节点使用**头插法**。Java1.8中变成了**尾插法**
7. Java1.8的hash()中，将hash值高位（前16位）参与到取模的运算中，使得计算结果的不确定性增强，降低发生哈希碰撞的概率

### HashMap扩容优化:

扩容以后,1.7对元素进行rehash算法,计算原来每个元素在扩容之后的哈希表中的位置,1.8借助2倍扩容机制,元素不需要进行重新计算位置

JDK 1.8 在扩容时并没有像 JDK 1.7 那样，重新计算每个元素的哈希值，而是通过高位运算**（e.hash & oldCap）**来确定元素是否需要移动，比如 key1 的信息如下：

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091744869.png)

使用 e.hash & oldCap 得到的结果，高一位为 0，当结果为 0 时表示元素在扩容时位置不会发生任何变化，而 key 2 信息如下:

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091744348.png)

高一位为 1，当结果为 1 时，表示元素在扩容时位置发生了变化，新的下标位置等于原下标位置 + 原数组长度hashmap，不必像1.7一样全部重新计算位置

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091744731.png)

### 为什么hashmap扩容的时候是两倍？

在存入元素时,放入元素位置有一个 (n-1)&hash 的一个算法,和hash&(newCap-1),这里用到了一个&位运算符

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091745298.png)

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091745758.png)

当HashMap的容量是16时，它的二进制是10000，(n-1)的二进制是01111，与hash值得计算结果如下

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091746177.png)

下面就来看一下HashMap的容量不是2的n次幂的情况，当容量为10时，二进制为01010，(n-1)的二进制是01001，向里面添加同样的元素，结果为：

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091746896.png)

可以看出，有三个不同的元素进过&运算得出了同样的结果，严重的hash碰撞了。

只有当n的值是2的N次幂的时候，进行&位运算的时候，才可以只看后几位，而不需要全部进行计算

### hashmap线程安全的方式

HashMap不是线程安全的,往往在写程序时需要通过一些方法来回避.其实JDK原生的提供了2种方法让HashMap支持线程安全.

**方法一：**通过Collections.synchronizedMap()返回一个新的Map,这个新的map就是线程安全的. 这个要求大家习惯基于接口编程,因为返回的并不是HashMap,而是一个Map的实现.

**方法二：**重新改写了HashMap,具体的可以查看java.util.concurrent.ConcurrentHashMap. 这个方法比方法一有了很大的改进.

**方法一特点：**

通过Collections.synchronizedMap()来封装所有不安全的HashMap的方法,就连toString, hashCode都进行了封装. 封装的关键点有2处,1)使用了经典的synchronized来进行互斥, 2)使用了代理模式new了一个新的类,这个类同样实现了Map接口.在Hashmap上面,synchronized锁住的是对象,所以第一个申请的得到锁,其他线程将进入阻塞,等待唤醒. 优点:代码实现十分简单,一看就懂.缺点:从锁的角度来看,方法一直接使用了锁住方法,基本上是锁住了尽可能大的代码块.性能会比较差.

**方法二特点:**

重新写了HashMap,比较大的改变有如下几点.使用了新的锁机制,把HashMap进行了拆分,拆分成了多个独立的块,这样在高并发的情况下减少了锁冲突的可能,使用的是NonfairSync. 这个特性调用CAS指令来确保原子性与互斥性.当如果多个线程恰好操作到同一个segment上面,那么只会有一个线程得到运行.

优点:需要互斥的代码段比较少,性能会比较好. ConcurrentHashMap把整个Map切分成了多个块,发生锁碰撞的几率大大降低,性能会比较好. 缺点:代码繁琐


## Java异常处理方式

Java 通过面向对象的方法进行异常处理，一旦方法抛出异常，系统自动根据该异常对象寻找合适异常处理器（Exception Handler）来处理该异常，把各种不同的异常进行分类，并提供了良好的接口。在 Java 中，每个异常都是一个对象，它是 Throwable 类或其子类的实例。当一个方法出现异常后便抛出一个异常对象，该对象中包含有异常信息，调用这个对象的方法可以捕获到这个异常并可以对其进行处理。Java 的异常处理是通过 5 个关键词来实现的：try、 catch、finally(捕获异常)、throw(抛出异常)、throws(声明异常)

在Java应用中，异常的处理机制分为声明异常，抛出异常和捕获异常。

> throw和throws的区别：
>
> 1. 位置不同：throw：方法内部；throws: 方法的签名处，方法的声明处
> 2. 内容不同：throw+异常对象（检查异常，运行时异常）；throws+异常的类型（可以多个类型，用，拼接）
> 3. 作用不同：throw：异常出现的源头，制造异常； throws:在方法的声明处，告诉方法的调用者，这个方法中可能会出现我声明的这些异常。然后调用者对这个异常进行处理：要么自己处理要么再继续向外抛出异常

### throws声明异常

通常，应该捕获那些知道如何处理的异常，将不知道如何处理的异常继续传递下去。传递异常可以在方法签名处使用 throws 关键字声明可能会抛出的异常。注意非检查异常（Error、RuntimeException 或它们的子类）不可使用 throws 关键字来声明要抛出的异常。 一个方法出现编译时异常，就需要 try-catch/ throws 处理，否则会导致编译错误

### throw抛出异常

如果你觉得解决不了某些异常问题，且不需要调用者处理，那么你可以抛出异常。 throw关键字作用是在方法内部抛出一个Throwable类型的异常。任何Java代码都可以通过throw语句抛出异常。

### try/catch捕获异常

程序通常在运行之前不报错，但是运行后可能会出现某些未知的错误，但是还不想直接抛出到上一级，那么就需要通过try…catch…的形式进行异常捕获，之后根据不同的异常情况来进行相应的处理。如何选择异常类型

![image-20230309175514154](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303091755223.png)

## 自定义异常在生产中如何应用

Java虽然提供了丰富的异常处理类，但是在项目中还会经常使用自定义异常，其主要原因是Java提供的异常类在某些情况下还是不能满足实际需球。例如以下情况：

- 系统中有些错误是符合Java语法，但不符合业务逻辑。
- 在分层的软件结构中，通常是在表现层统一对系统其他层次的异常进行捕获处理。

## 什么是字节码

因为JVM针对各种操作系统和平台都进行了定制，无论在什么平台，都可以通过javac命令将一个.java文件编译成固定格式的字节码（.class文件）供JVM使用。之所以被称为字节码，是因为**.class文件是由十六进制值组成的，JVM以两个十六进制值为一组，就是以字节为单位进行读取**。

下图为一个例子

![请添加图片描述](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131259250.png)

## 字节码的组成结构

JVM对字节码的规范是有要求的，要求每一个字节码文件都要有十部分固定的顺序组成，如下图：

![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131257067.png)

### 魔数

所有的.class文件的前4个字节都是魔数，魔数以一个固定值：0xCAFEBABE，放在文件的开头，JVM就可以根据这个文件的开头来判断这个文件是否可能是一个.class文件，如果是以这个开头，才会往后执行下面的操作，这个魔数的固定值是Java之父James Gosling指定的，意为CafeBabe（咖啡宝贝）

### 版本号

版本号是魔数之后的4个字节，前两个字节表示次版本号（Minor Version），后两个字节表示主版本号（Major Version），上面的0000 0032，次版本号0000转为十进制是0，主版本号0032 转为十进制50，对应下图的版本映射关系，可以看到对应的java版本号是1.6


![img](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131300195.png)

### 常量池

紧接着主版本号之后的字节为常量池入口，常量池中有两类常量：字面量和符号引用，字面量是代码中申明为Final的常量值，符号引用是如类和接口的全局限定名、字段的名称和描述符、方法的名称和描述符。常量池整体分为两个部分：常量池计数器以及常量池数据区

![image-20230313130501643](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131305727.png)

### 访问标志

常量池结束后的两个字节，描述的是类还是接口，以及是否被Public、Abstract、Final等修饰符修饰，JVM规范规定了9种访问标示（Access_Flag）JVM是通过按位或操作来描述所有的访问标示的，比如类的修饰符是Public Final，则对应的访问修饰符的值为ACC_PUBLIC | ACC_FINAL，即0x0001 | 0x0010=0x0011

![image-20230313130903338](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131309090.png)

### 当前类索引

访问标志后的两个字节，描述的是当前类的全限定名，这两个字节保存的值是常量池中的索引值，根据索引值就能在常量池中找到这个类的全限定名

### 父类索引

当前类名后的两个字节，描述的父类的全限定名，也是保存的常量池中的索引值

### 接口索引

父类名称后的两个字节，是接口计数器，描述了该类或者父类实现的接口数量，紧接着的n个字节是所有接口名称的字符串常量的索引值

### 字段表

用于描述类和接口中声明的变量，包含类级别的变量和实例变量，但是不包含方法内部声明的局部变量，字段表也分为两个部分，第一部分是两个字节，描述字段个数，第二部分是每个字段的详细信息fields_info

![image-20230313131126270](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131311354.png)

### 方法表

字段表结束后为方法表，方法表也分为两个部分，第一个部分是两个字节表述方法的个数，第二部分是每个方法的详细信息
方法的访问信息比较复杂，包括方法的访问标志、方法名、方法的描述符和方法的属性：

![image-20230313131419946](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131314025.png)

### 附加属性

字节码的最后一部分，该项存放了在该文件中类或接口所定义属性的基本信息。

## class初始化过程

首先类加载的机制过程分为5个部分：加载、验证、准备、解析、初始化

![image-20230313131524170](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202303131315241.png)

- 类的初始化阶段，是真正开始执行类中定义的java程序代码(字节码)并按程序员的意图去初始化类变量的过程。更直接地说，初始化阶段就是执行类构造器()方法的过程。()方法是由编译器自动收集类中的所有类变量的赋值动作和静态代码块static{}中的语句合并产生的，其中编译器收集的顺序是由语句在源文件中出现的顺序所决定。
- 关于类初始化的顺序**（静态变量、静态初始化块：决于它们在类中出现的先后顺序）>（变量、初始化块：决于它们在类中出现的先后顺序）>构造器**
- 关于类初始化的详细过程，参见 Java虚拟机规范一书中，其中类初始化过程如下：
  1. 每个类都有一个初始化锁LC，进程获取LC，这个操作会导致当前线程一直等待，直到获取到LC锁
  2. 如果C正在被其他线程初始化，当前线程会释放LC进去阻塞状态，并等待C初始化完成。此时当前线程需要重试这一过程。执行初始化过程时，线程的中断状态不受影响
  3. 如果C正在被本线程初始化，即递归初始化，释放LC并且正常返回
  4. 如果C已经被初始化完成，释放LC并且正常返回
  5. 如果C处于错误状态，表明不可能再完成初始化，释放LC并抛出异常NoClassDefFoundError异常
  6. 否则，将C标记为正在被本线程初始化，释放LC；然后，初始化那些final且为基础类型的类成员变量
  7. 如果C是类而不是接口，且C的父类Super Class（SC）和各个接口SI_n（按照implements子句中的顺序来）还没有初始化，那么就在SC上面递归地进行完整的初始化过程，如果有必要，需要先验证和准备SC ；如果SC或SIn初始化过程中抛出异常，则获取LC，将C标记为错误状态，并通知所有正在等待的线程，然后释放LC，然后再抛出同样的异常。
  8. 从C的classloader处获取assertion断言机制是否被打开
  9. 接下来，按照文本顺序执行类变量初始化和静态代码块，或接口的字段初始化，把它们当作是一个个单独的代码块。
  10. 如果执行正常，那就获取LC，标记C对象为已初始化，并通知所有正在等待的线程，然后释放LC，正常退出整个过程
  11. 否则，如果抛出了异常E那么会中断退出。若E不是Error，则以E为参数创建新的异常ExceptionInInitializerError作为E。如果因为OutOfMemoryError导致无法创建ExceptionInInitializerError，则将OutOfMemoryError作为E。
  12. 获取LC，将C标记为错误状态，通知所有等待的线程，释放LC，并抛出异常E。

可以看到 JLS确实规定了父类先初始化、static块和类变量赋值按照文本顺序来
