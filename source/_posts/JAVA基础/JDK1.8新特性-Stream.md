---
title: JDK1.8新特性 Stream
categories: java基础
date: 2023-11-08 15:22:33
tags: 
  - java
  - JDK1.8
---

## 传统集合的遍历

几乎所有的集合（如`Collection`接口或`Map`接口等）都支持直接或间接的遍历操作。而当我们需要对集合中的元素进行操作的时候，除了必需的添加、删除、获取外，最典型的就是集合遍历，如：

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("159");
	list.add("111");
	list.add("373");
	list.add("10");
	list.add("101");
	list.add("564");	
    for (String s : list) {
        System.out.println(s);
    }
}
```

**循环遍历的弊端：**

Java 8的`Lambda`让我们可以更加专注于**做什么**（What），而不是**怎么做**（How），这点此前已经结合内部类进行了对比说明。现在，我们仔细体会一下上例代码，可以发现：

- for循环的语法就是“**怎么做**”
- for循环的循环体才是“**做什么**”

 为什么使用循环？因为要进行遍历。但循环是遍历的唯一方式吗？遍历是指每一个元素逐一进行处理，**而并不是从第一个到最后一个顺次处理的循环**。前者是目的，后者是方式。

如果我们要将集合A根据条件一过滤为子集B，然后根据条件二过滤为子集C，那么按照传统的方式就是遍历加遍历。每当我们需要对集合中的元素进行操作的时候，总是需要进行循环、循环、再循环。循环是做事情的方式，而不是目的。另一方面，使用线性循环就意味着只能遍历一次。如果希望再次遍历，只能再使用另一个循环从头开始。

对此，JDK1.8提供了`Stream`，我们不再需要遍历，而是将其转换为流进行操作：

```java
list.stream().filter(s -> s.startsWith("1")).filter(s -> s.length() == 3).forEach(System.out::println);
```

其流程为：获取流、过滤首位为1、过滤长度为3、逐一打印。

## Stream流式思想

**”Stream 流“ 其实是一个集合元素的函数模型，它不是集合，也不是数据结构**

Stream（流）是一个来自数据源的元素队列：

- 元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算
- **数据源**流的来源可以是集合，数组等Collection对象等。

和`Collection`操作不同， Stream操作还有两个基础的特征：

- **Pipelining**: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
- **内部迭代**： 以前对集合遍历都是通过Iterator或者增强for的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式，流可以直接调用遍历方法。

当使用一个流的时候，通常包括三个基本步骤：获取一个数据源（source）→ 数据转换→执行操作获取想要的结果，每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道。

## 获取Stream流

`java.util.stream.Stream<T>`是Java 8新加入的最常用的流接口（这并不是一个函数式接口）

- 所有的`Collection`集合都可以通过`Stream`默认方法获取流
- `Stream`接口的静态方法`of`可以获取数组对应的流

### 根据 `Collection` 获取流

`java.util.Collection`接口中加入了default方法`stream`用来获取流，所以其所有实现类均可获取流

```java
class Test {
    public static void main(String[] args) {
        List<String> list = null;
        list.stream(); // List 的Stream 方法

        Set<String> set = null;
        set.stream(); // Set 的 Stream 方法
    }
}
```

### Map 获取流

`java.util.Map`接口不是`Collection`的子接口，且其K-V数据结构不符合流元素的单一特征，所以获取对应的流需要分key、value或entry等情况：

```java
class MapDemo {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        //......
        Stream<String> keyStream = map.keySet().stream();
        Stream<String> valueStream = map.values().stream();
        Stream<Map.Entry<String, String>> entryStream = map.entrySet().stream();
    }
}
```

### 数组获取流

由于数组对象不可能添加默认方法，所以`Stream`接口中提供了静态方法`of`，`of`方法的参数其实是一个可变参数，所以支持数组

```java
class ArrayDemo {
    public static void main(String[] args) {
        String[] array = {"11", "12", "132", "244", "25", "26", "347"};
        Stream<String> stream = Stream.of(array);  //  数组获取流
        Arrays.stream(array).filter(s -> s.startsWith("1")).filter(s -> s.length() == 2).forEach(s -> System.out.println(s));
    }
}
```

## Stream流操作方法

```java
 Arrays.stream(array)											// 创建Stream
     .filter(s -> s.startsWith("1")).filter(s -> s.length() == 2)	// 转换Stream(对流进行筛选过滤)
     .forEach(s -> System.out.println(s));						  // 聚合(得到结果)
```

流模型的操作方法可以被分为两类：

- **延迟方法**：返回值类型仍然是`Stream`接口自身类型的方法，因此支持链式调用。
- **终结方法**：返回值类型不再是`Stream`接口自身类型的方法，因此不再支持类似`StringBuilder`那样的链式调用。

除了终结方法外，其余方法均为延迟方法。

常用的方法如下：

| 方法名称 | 方法作用   | 方法种类 |
| -------- | ---------- | -------- |
| count    | 统计个数   | 终结方法 |
| forEach  | 逐一处理   | 终结方法 |
| filter   | 过滤       | 延迟方法 |
| limit    | 取用前几个 | 延迟方法 |
| skip     | 跳过前几个 | 延迟方法 |
| map      | 映射       | 延迟方法 |
| concat   | 组合       | 延迟方法 |

### count

 `long count ()` 统计流中的元素，返回long类型数据

```java
long count = list.stream().count(); 	 // 返回值为list大小
```

### forEach

` void forEach(Consumer<? super T> action)`：逐一处理流中的元素

参数 `Consumer<? super T> action`：函数式接口，只有一个抽象方法：`void accept（T t)`；

- 此方法并不保证元素的逐一消费动作在流中是有序进行的（元素可能丢失）
- Consumer是一个消费接口（可以获取流中的元素进行遍历操作，输出出去），可以使用Lambda表达式

```java
list.stream().forEach(name -> System.out.println(name));
```

### filter

- `filter` 方法是将一个流转换成另一个子集流
- 该接口接收一个`Predicate`函数式接口参数（可以是一个Lambda或方法引用）作为筛选条件
- `Stream<T> filter(Predicate<? super T> predicate);`

```java
list.stream().filter( a -> a.startsWith("1"))
```

### limit

`Stream<T> limit(long maxSize)`;  取用前几个元素

参数是一个long 类型，如果流的长度大于参数，则进行截取；否则不进行操作

```java
list.stream().limit(3).forEach(name -> System.out.println(name));
```

### skip

`Stream<T> skip(long n)`   跳过前几个元素

如果流的当前长度大于n，则跳过前n个，否则将会得到一个长度为0的空流

```java
list.stream().skip(3).forEach(name -> System.out.println(name));
```

### map

  `<r> Stream <R> map(Function<? super T,? exception R> mapper` ： 将一个流中的元素映射到另一个流中

- 将流中的元素映射到另一个流中
- 参数Function<T,R>：函数式接口，抽象方法：R apply(T t);
- 该接口需要一个`Function`函数式接口参数，可以将当前流中的T类型数据转换为另一种R类型的流

```java
 list.stream().map(s -> Integer.parseInt(s)).forEach(s -> System.out.println(s));
```

### concat

`  public static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)` ：  两个流合并成为一个流

- 这是一个静态方法，与`java.lang.String`当中的`concat`方法是不同的

```java
public static void main(String[] args) {
	Stream<String> streamA = Stream.of("1", "2", "3");
	Stream<String> streamB = Stream.of("4", "5", "6");
	Stream.concat(streamA, streamB).forEach(s -> System.out.println(s));
}
```

## 收集Stream流

Stream流中提供了一个方法，可以把流中的数据收集到单例集合中

- <R, A> R collect(Collector<? super T, A, R> collector);   把流中的数据收集到单例集合中
- 返回值类型是R。R指定为什么类型，就是收集到什么类型的集合
- 参数`Collector<? super T, A, R>`中的R类型，决定把流中的元素收集到哪个集合中
- 可以使用` java.util.stream.Collectors`工具类中的静态方法得到参数`Collector`

```java
// stream 收集到单列集合中
List<String> list = stream.collect(Collectors.toList());
// stream 手机到单列集合中
Set<String> set = stream.collect(Collectors.toSet());
```

