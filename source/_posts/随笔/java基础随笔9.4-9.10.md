---
title: java基础随笔9.4-9.10
categories: 随笔
tags: 
  - java基础
---

# Vector类

## 简介

Vecrtor类实现了一个动态数组。和 ArrayList 很相似，但是两者是不同的：

- Vector 是同步访问的。
- Vector 包含了许多传统的方法，这些方法不属于集合框架。

Vector 主要用在事先不知道数组的大小，或者只是需要一个可以改变大小的数组的情况。

## 构造方法

```java
// 第一种构造方法创建一个默认的向量，默认大小为 10：
new Vector();

// 第二种构造方法创建指定大小的向量。
new Vector(int size);

// 第三种构造方法创建指定大小的向量，并且增量用 incr 指定。增量表示向量每次增加的元素数目。
new Vector(int size,int incr);

// 第四种构造方法创建一个包含集合 c 元素的向量：
new Vector(Collection c);
```

![image-20230910190607958](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101906044.png)

![image-20230910190641863](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101906945.png)

```java
import java.util.*;

public class VectorDemo {

   public static void main(String args[]) {
      // initial size is 3, increment is 2
      Vector v = new Vector(3, 2);
      System.out.println("Initial size: " + v.size());
      System.out.println("Initial capacity: " +
      v.capacity());
      v.addElement(new Integer(1));
      v.addElement(new Integer(2));
      v.addElement(new Integer(3));
      v.addElement(new Integer(4));
      System.out.println("Capacity after four additions: " +
          v.capacity());

      v.addElement(new Double(5.45));
      System.out.println("Current capacity: " +
      v.capacity());
      v.addElement(new Double(6.08));
      v.addElement(new Integer(7));
      System.out.println("Current capacity: " +
      v.capacity());
      v.addElement(new Float(9.4));
      v.addElement(new Integer(10));
      System.out.println("Current capacity: " +
      v.capacity());
      v.addElement(new Integer(11));
      v.addElement(new Integer(12));
      System.out.println("First element: " +
         (Integer)v.firstElement());
      System.out.println("Last element: " +
         (Integer)v.lastElement());
      if(v.contains(new Integer(3)))
         System.out.println("Vector contains 3.");
      // enumerate the elements in the vector.
      Enumeration vEnum = v.elements();
      System.out.println("\nElements in vector:");
      while(vEnum.hasMoreElements())
         System.out.print(vEnum.nextElement() + " ");
      System.out.println();
   }
}
```

执行结果为：

```java
Initial size: 0
Initial capacity: 3
Capacity after four additions: 5
Current capacity: 5
Current capacity: 7
Current capacity: 9
First element: 1
Last element: 12
Vector contains 3.

Elements in vector:
1 2 3 4 5.45 6.08 7 9.4 10 11 12

```

# String  split()方法

split()方法根据匹配给定的正则表达式来拆分字符串

**注意：** 

- `.`、 `$`、 `|` 和 `*` 等转义字符，必须得加 `\\`。
- 多个分隔符，可以用 `|` 作为连字符。

## 语法

```java
/**
 * @param: regex  正则表达式分隔符（遇到什么符号就分割一次）
 * @param: limit  分割的份数（最大分割成limit个字符串，也就是最多分割limit-1次）
 * @return: 分割后得到的字符串数组
 */
public String[] split(String regex, int limit)
```

## 例子

```java
String str2 = new String("www.runoob.com");
System.out.println("转义字符返回值 :" );
for (String retval: str2.split("\\.", 3)){
	System.out.println(retval);
}
 

String str3 = new String("acount=? and uu =? or n=?");
System.out.println("多个分隔符返回值 :" );
for (String retval: str3.split("and|or")){
	System.out.println(retval);
}
```

上述例子的结果为

```java
转义字符返回值 :
www
runoob
com

// uu =? 和 n=? 的前后都有空格 
多个分隔符返回值 :
acount=? 
 uu =? 
 n=?
```

# HashMap  `computeIfAbsent()` 方法

`computeIfAbsent()` 方法对 hashMap 中指定 key 的值进行重新计算，如果不存在这个 key，则添加到 hashMap 中。

## 语法

```java
/**
 * @param: key  键
 * @param: remappingFunction  重新映射函数，用于重新计算值
 * @return: 如果key对应value不存在，则使用remappingFunction重新计算后的值，并保存为该key的value，否则返回 value。
 */
hashmap.computeIfAbsent(K key, Function remappingFunction)
```

## 实例

```java
public static void main(String[] args) {
	// 创建一个 HashMap
	HashMap<String, Integer> prices = new HashMap<>();

	// 往HashMap中添加映射项
	prices.put("Shoes", 200);
	prices.put("Bag", 300);
	prices.put("Pant", 150);
	System.out.println("HashMap: " + prices);

	// 计算 Shirt 的值
	int shirtPrice = prices.computeIfAbsent("Shirt", key -> 280);
	System.out.println("Price of Shirt: " + shirtPrice);

	// 计算 Bag 的值
	int bagPrice = prices.computeIfAbsent("Bag", key -> 200);
	System.out.println("Price of Bag: " + bagPrice);

	// 输出更新后的HashMap
	System.out.println("Updated HashMap: " + prices);
}
```

上述实例的输出结果为

```json
HashMap: {Pant=150, Bag=300, Shoes=200}
Price of Shirt: 280
Price of Bag: 300
Updated HashMap: {Pant=150, Shirt=280, Bag=300, Shoes=200}
```

Shirt的映射关系不存在，所以调用计算函数`key -> 280`，将key值的value设为280并存储在map中；Bag的映射关系已经存在，所以直接返回200。这里的计算函数是一个Lambda表达式

# String trim()方法

本文参考自[5分钟了解一下，String.trim()到底做了什么事](https://blog.csdn.net/zhuzicc/article/details/121588517)

## 前言

印象中`trim()`函数就是用作去除字符串首尾空格，如下

```java
String str = " Hello World ";
System.out.println("trim()操作前字符串长度：" + str.length());
System.out.println("trim()操作后字符串长度：" + str.trim().length());

// 输出结果
trim()操作前字符串长度：13
trim()操作后字符串长度：11
```

这里进行一个小插曲:

Java 语言规范规定，Java 的 char 类型是 UTF-16 的 code unit，也就是一定是16位（2字节）。

char(字符)的范围是：0-65535 or(`\u0000`~`\uFFFF`)

其中空格键在Ascii表中对应的是32也就是`\u0020`

## 进入源码

![image-20230910132305141](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101323539.png)

 其大致意思是

- 删除任何前置和后置空格（其实是小于`\u0020`的字符）；
- 如果String对象表示一个空字符串，则返回对这个String对象的引用（返回一个新对象）；
- 如果String对象表示的字符的首和尾字符的编码都大于`\u0020`(空格字符)，则返回对这个String对象的引用；
- 如果String字符串中没有编码大于`\u0020`的字符，则返回一个表示空字符串的string对象；
- 假设 k 是大于`\u0020`的字符串中第一个字符的索引，假设 m 是大于`\u0020`的字符串中最后一个字符的索引。返回一个表示该字符串的子字符串，该子字符串以下标 k 处的字符开始，以下标 m 处的字符结束。即 substring(k, m + 1)。
- 如果使用`trim()`后的字符串长度不等于使用前的长度，就返回一个新String对象给你；如果等于，就还给你原String对象.

下面是对应规则的例子

```java
  /**
	* 1.删除任何前置和后置空格；
	*/
	@Test
	public void str1(){
	   String str = " Hello World ";
	   System.out.println(str.length());					// 13
	   System.out.println("trim()：" + str.trim().length());	 // 11
	}

   /**
     * 你这有一个空字符串，我换一个新的对象给你
     */
    @Test
    public void str2(){
        // 如果String对象表示一个空字符串，则返回对这个String对象的引用；
        String str1 = " ";	
        System.out.println(str1.length());	// 1
        String str2 = str1.trim();    		
        System.out.println("trim()->" + str2.length()); // trim()->0
        System.out.println(str1 == str2);	// false
    }


   /**
     * 空格前后有内容，就不删除
     */
    @Test
    public void str3(){
        String str1 = "aaa bbb ccc";
        System.out.println(str1.length());		// 11
        String str2 = str1.trim();
        System.out.println(str2.length());		// 11
    }

  /**
    * String字符串中的字符都小于\u0020,返回一个新的空字符串对象
    */
    @Test
    public void str4(){
        // \u0000 - \u0031
        char[] chars = new char[32];
        for (int i = 0; i < 32; i++) {
            chars[i] = (char) i;
        }
        String oldStr = new String(chars);
        System.out.println(oldStr.length());   		// 32
        String newStr = oldStr.trim();
        System.out.println(newStr.trim().length());	 // 0
        System.out.println(oldStr == newStr);  		// false
    }


   /**
     * 假设 k 是代码大于’\u0020’的字符串中第一个字符的索引，
     * 假设 m 是代码大于’\u0020’的字符串中最后一个字符的索引。
     * 返回一个表示该字符串的子字符串，该子字符串以下标 k 处的
     * 字符开始，以下标m处的字符结束。即substring(k, m + 1)。
     */
    @Test
    public void str5(){
        // 长度为8
        char[] chars = new char[8];
        // 前三个小于\u0020
        for (int i = 0; i < 3; i++) {
            chars[i] = (char) i;
        }
        chars[3] = 65;
        chars[4] = 66;
        // 第6个小于\u0020
        chars[5] = 31;
        chars[6] = 68;
        // 最后一个也小于\u0020
        chars[7] = 21;
        String oldStr = new String(chars);
        System.out.println("oldStr.length()：" + oldStr.length());	// 8
        System.out.println("oldStr：" + oldStr);			//  ABD
        String newStr = oldStr.trim();	
        System.out.println("newStr.length()：" + newStr.length());	// 4
        System.out.println("newStr：" + newStr);			//  ABD
    }


   /**
     * 长度等于原来的，返回原字符串对象，否则新建一个字符串
     */
    @Test
    public void str6(){
        // 长度为8
        char[] chars = new char[5];
        // 前三个小于\u0020
        for (int i = 0; i < 3; i++) {
            chars[i] = (char) i;
        }
        chars[3] = 65;
        chars[4] = 66;
        String str1 = new String(chars);
        String str2 = "ABCDE";

        String newStr1 = str1.trim();
        String newStr2 = str2.trim();


        System.out.println("str1.length()：" + str1.length());   	// 5
        System.out.println("newStr1.length()：" + newStr1.length());	// 2
        System.out.println(str1 == newStr1);			// 5 != 2  所以为false
        System.out.println("=========================");

        System.out.println("str2.length()：" + str2.length());		// 5
        System.out.println("newStr2.length()：" + newStr2.length());	// 5
        System.out.println(str2 == newStr2);			//5 == 5  所以为true
    }
```

所以`trim()`到底干了啥

- 去除 `String` 中的首尾空格；
- 无法去除字符串中间含有空格的；
- 准确说实际去除的是小于十进制32（32就是空格，可以去看Ascii表）的所有字符。
- 当 `String` 中全是小于32的字符时，返回一个新的字符给你。
- `String.trim()` 长度变化就是新对象，无变化就还是自己。

# AtomicInteger

参考自[原子操作类AtomicInteger详解](https://blog.csdn.net/fanrenxiang/article/details/80623884)

Interger的原子操作类

### 为什么要使用原子操作类

对于Java中的运算操作，例如自增或自减，若没有进行额外的同步操作，在[多线程](https://so.csdn.net/so/search?q=多线程&spm=1001.2101.3001.7020)环境下就是线程不安全的。num++解析为num=num+1，明显，这个操作不具备原子性，多线程并发共享这个变量时必然会出现问题。测试代码如下:

```java
public class AtomicIntegerTest {

    private static final int THREADS_CONUT = 20;
    public static int count = 0;

    public static void increase() {
        count++;
    }

    public static void main(String[] args) {
        Thread[] threads = new Thread[THREADS_CONUT];
        for (int i = 0; i < THREADS_CONUT; i++) {
            threads[i] = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 1000; i++) {
                        increase();
                    }
                }
            });
            threads[i].start();
        }

        while (Thread.activeCount() > 1) {
            Thread.yield();
        }
        System.out.println(count);
    }
}
```

这里运行了20个线程，每个线程对count变量进行1000此自增操作，如果上面这段代码能够正常并发的话，最后的结果应该是20000才对，但实际结果却发现每次运行的结果都不相同，都是一个小于20000的数字。这是为什么呢？

## 如果换成Volatile修饰count变量呢

顺带说下volatile关键字很重要的两个特性:

- 保证变量在线程间可见，对volatile变量所有的写操作都能立即反应到其他线程中，换句话说，volatile变量在各个线程中是一致的（得益于java内存模型—"先行发生原则"）；
- 禁止指令的重排序优化；

那么换成volatile修饰count变量后，会有什么效果呢?

```java
public class AtomicIntegerTest {
 
    private static final int THREADS_CONUT = 20;
    public static volatile int count = 0;
 
    public static void increase() {
        count++;
    }
 
    public static void main(String[] args) {
        Thread[] threads = new Thread[THREADS_CONUT];
        for (int i = 0; i < THREADS_CONUT; i++) {
            threads[i] = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 1000; i++) {
                        increase();
                    }
                }
            });
            threads[i].start();
        }
 
        while (Thread.activeCount() > 1) {
            Thread.yield();
        }
        System.out.println(count);
    }
}
```

结果似乎又失望了，测试结果和上面的一致，每次都是输出小于20000的数字。这又是为什么么？ 上面的论据是正确的，也就是上面标红的内容，但是这个论据并不能得出"基于volatile变量的运算在并发下是安全的"这个结论，因为核心点在于java里的运算（比如自增）并不是原子性的。

### 用了AtomicInteger类后会变成什么样子呢？

把上面的代码改造成AtomicInteger原子类型，先看看效果

```java
import java.util.concurrent.atomic.AtomicInteger;
 
public class AtomicIntegerTest {
 
    private static final int THREADS_CONUT = 20;
    public static AtomicInteger count = new AtomicInteger(0);
 
    public static void increase() {
        count.incrementAndGet();
    }
 
    public static void main(String[] args) {
        Thread[] threads = new Thread[THREADS_CONUT];
        for (int i = 0; i < THREADS_CONUT; i++) {
            threads[i] = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 1000; i++) {
                        increase();
                    }
                }
            });
            threads[i].start();
        }
 
        while (Thread.activeCount() > 1) {
            Thread.yield();
        }
        System.out.println(count);
    }
}
```

结果每次都输出20000，程序输出了正确的结果，这都归功于AtomicInteger.incrementAndGet()方法的原子性。

暂时先了解到这里，后续可以在开头引用链接处更加深入了解

# chain.doFilter(request,response)

过滤器的生命周期一般要经过以下三个阶段

## 初始化：

当容器第一次加载该过滤器时，init()方法将被调用。该类在这个方法中包含了一个指向`Filter Config`对象的引用

## 过滤：

过滤器的大多时间都消耗在这里，doFilter方法被容器调用，同时传入分别指向这个请求/响应链中的Servlet Request、Servlet Response和Filter Chain对象的引用，然后过滤器就有机会处理请求，将处理任务传递给链中的下一个资源(通过调用Filter Chain对象引用上的doFilter方法)，之后在处理控制权返回该过滤器时处理响应

## 析构

容器紧跟着在垃圾收集之前调用destroy()方法，以便能够执行任何必须的清理代码



**关于chain.doFilter(request, response)**

他的作用是请求转发发给过滤器链上的下一个对象。这里的下一个指的是下一个Filter，如果没有filter那他就是你请求的资源。一般filter都是一个链，web.xml里面配置了几个就有几个。一个一个的连在一起`request -> filter1 -> filter2 -> filter3... -> request resource`