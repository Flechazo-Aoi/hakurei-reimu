---
title: Java中的基本数据类型与包装类及之间的比较（==）
categories: java基础
tags: 
- java
- 随笔
---

本文参考https://blog.csdn.net/qq_26287435/article/details/107852241

## 基本数据类型与包装数据类型

原始数据类型也就是基本数据类型，八种基本数据类型和其包装类分别如下，其中包装类为引用数据类型

- byte，java.lang.Byte（父类Number）
- short，java.lang.Short（父类Number）
- int，java.lang.Integer（父类Number）
- long，java.lang.Long（父类Number）
- boolean，java.lang.Boolean（父类Object）
- char，java.lang.Character（父类Object）
- float，java.lang.Float（父类Number）
- double，java.lang.Double（父类Number）

## 两种数据类型的==比较

Java中的`基本类型`及其`包装类的比较（==）`一直是一个比较头疼的问题，不仅有`自动装箱和拆箱`操作，部分的包装类还有对象`缓存池`，这就导致了这部分知识容易混淆。

这是因为对于`==`操作符来说，如果比较的数据是`基本类型`，则比较它们的`值`，如果比较的是`对象`，则会比较`对象的内存地址`。另外，如果一个是基本类型、一个是包装类型，在比较前会先把包装类型`拆箱`成基本类型，然后进行比较。

以int为例，这里我们把参与比较的类型分为三种：`int`、直接`new出来的Integer对象`和`自动装箱出来的Integer对象`。这里先不考虑`Integer的缓存池`，我们对三种类型之间的`两两比较`进行下排列组合，3 * 3一共有`9`种可能。由于`==`运算符是`不区分左右的先后顺序`的，也就是说`a == b`跟`b == a`等价，所以可以去除3种重复比较，这样就只有6种情况了。具体如下表：

| 类型/类型          | **int** | **new Integer** | **Integer AutoBoxing** |
| ------------------ | ------- | --------------- | ---------------------- |
| int                | ①       | ②               | ④                      |
| new Integer        | ②       | ③               | ⑤                      |
| Integer AutoBoxing | ④       | ⑤               | ⑥                      |

### int  ==  int

```java
// int 与 int 类型比较
int i1 = 128;
int i2 = 128;
System.out.println("int == int, result:" + (i1 == i2));// true
System.out.println("----------------------------------------------------");
```

基本数据类型比较的是值，所以毫无疑问是true

###   int  ==   new Integer 

```java
// int类型和new出来的Integer对象比较
int i3 = 128;
Integer i4 = new Integer(128);
// 在进行运算前，i4会自动拆箱，实质是两个int类型比较
System.out.println("int == new Integer, result:" + (i3 == i4));// true
System.out.println("----------------------------------------------------");
```

i4在比较时会自动拆箱成基本数据类型，所以也是值比较，结果为true

### new Integer    ==  new Integer 

```java
// 两个new出来的Integer对象的内存地址比较
Integer i5 = new Integer(128);
Integer i6 = new Integer(128);
System.out.println("new Integer == new Integer, result:" + (i5 == i6)  // false
System.out.println("----------------------------------------------------");

```

包装数据类型的比较的是对象的地址，因为是两个不同的对象，所以为false

### int  ==   Integer AutoBoxing

```java
// ③int类型 和 自动装箱的Integer对象 比较
int i7 = 128;
Integer i8 = 128;
// 在进行运算前，i8会自动拆箱，实质是两个int类型比较
System.out.println("int == Integer autoBoxing, result:" + (i7 == i8));// true
System.out.println("----------------------------------------------------");
```

i8在赋值的时候，会自动把128装箱成Integer类型，接着使用`==`比较的时候，i8又会自动拆箱成int，所以实质上还是两个`int`在比较

### Integer AutoBoxing   ==  new Integer 

```java
// 自动装箱的Integer类型 和 new出来的Integer对象 比较
Integer i9 = 128;
Integer i10 = new Integer(128);
System.out.println("Integer autoBoxing == new Integer, result:" + (i9 == i10));// false
System.out.println("----------------------------------------------------");
```

i9在赋值时会将128自动装箱成Interger类型，比较时是两个不同的对象比较，地址必然不同，为false

### Integer AutoBoxing   ==   Integer AutoBoxing

```java
// 自动装箱的Integer对象 和 自动装箱的Integer对象 比较，区间[-128, 127]之外
Integer i11 = 128;
Integer i12 = 128;
System.out.println("Integer autoBoxing == Integer autoBoxing, 区间[-128, 127]之外，result:" + (i11 == i12));// false
System.out.println("----------------------------------------------------");
```

这里的i10和i11在赋值时都会先将128`自动装箱成Integer类型`，然后再进行比较，原理同上，还是为false

上述涉及到自动装箱的Integer对象时用到的数字均为128，这是为了不受Integer缓存池的影响

## Integer的缓存池

```java
// 自动装箱会从缓存池中取对象，缓存池的区间为[-128, 127]
// 自动装箱的Integer对象 和 自动装箱的Integer对象 比较，区间[-128, 127]中
Integer i13 = 127;// 缓存池
Integer i14 = 127;// 缓存池
System.out.println("Integer autoBoxing == Integer autoBoxing, 区间[-128, 127]中，result:" + (i13 == i14));// true
```

这个例子跟上面的最后一种情况一摸一样，只是把数字换成了127，结果就变成了true。

过程是：i13和i14的赋值过程中，均会触发自动装箱。给127生成对应的`Integer对象`，接下来使用`==`比较这两个`Integer对象的地址`，最后结果为true，也就是说明i13和i14是同一个对象，分配的是同一块内存

Integer对应的自动装箱的方法是`Integer#valueOf()`，我们看下它的实现:

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

可以看到如果i的值在某个范围内的话，就会从`IntegerCache.cache`这个数组中取出一个`Integer对象`返回；如果超出了这个范围的话就直接`new`一个`Integer对象`返回。

省略掉无关代码，`IntegerCache`的具体实现如下

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        ...
        high = h;

        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```

这里的`下限low`的值默认为`-128`，`上限high`的值默认为`127`，`cache`是一个`Integer类型的数组`。

`static`代码块里面主要做了两件事：

- 初始化`cache`这个Integer数组，数组容量为256。
- 给`cache`的所有元素赋值。在for循环里面，依次给cache的各个元素进行赋值，Integer的值从-128开始，一直到127，对应的是闭区间[-128, 127]，刚好256个元素。

所以我们常说的`Integer的缓存池`其实就是这里的`IntegerCache.cache`，它在Integer进行`类加载`的时候初始化.

在基本类型int需要自动装箱的时候，就会调用Integer#valueOf方法，在Integer#valueOf方法的实现中，如果int的值在闭区间[-128, 127]（IntegerCache.cache缓存的范围内），就返回已经构建好的Integer对象，具体是根据角标从IntegerCache.cache这个数组中去取。

经过上面的讲解可以知道，我们这里的`127`经过`自动装箱`后，是从缓存池中拿的Integer对象，所以`i13`和`i14`拿到的是同一个Integer对象（因为它们的值相同，在`IntegerCache.cache`数组中对应的index相同，所以获取到的是同一个缓存对象）。

基本类型对应的包装类中，`Byte`、`Short`、`Integer`、`Long`和`Character`的缓存池都是`[-128, 127]`，`Boolean`的缓存池比较特殊，只有true和false两个Boolean对象。`Float`和`Double`没有缓存池