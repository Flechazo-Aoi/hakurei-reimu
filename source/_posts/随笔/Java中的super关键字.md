---
title: Java中的super关键字
categories: 随笔
tags: 
- java
---

本文参考自https://blog.csdn.net/pipizhen_/article/details/107165618

## this关键字

- 能出现在实例方法和构造方法中

- 语法是`this.`和`this()`

- `this`不能出现在静态方法中（比如`main()`方法中）

- `this()`只能出现在构造方法的第一行，通过当前的构造方法去调用“**本类**”中的对应的构造方法，目的是：代码复用。

- `this`在大部分情况下是可以省略的

  在区分局部变量和实例变量的情况下不能省略，如下

  ```java
  Public void setName(String name){
  	this.name = name;
  }
  ```

## supper关键字

- 能出现在实例方法和构造方法中。

- 语法是`super.`和`super()`

- `super`不能出现在静态方法中（比如`main()`方法中）

- `super`在大部分情况下是可以省略的

  那么`supper`在什么情况下是不可以省略的呢？

  对比`this`，`this`指向的是对象自己。`supper`指向的是当前对象自己的父类型特征（也就是继承过来的东西：属性，方法等）

  `super`和`this`区别是：this可以看做一个引用变量，保存了该对象的地址，是当前对象整体，而`super`代表的是父类型特征，是子类局部的一些东西，这些继承过来的东西已经在子类里面了，你可以输出整体`this`，但不能输出父类型特征`super`。因为`super`指向的东西不是一个整体，没法打印输出。

  ```java
  System.out.println(this);  // 输出this.toString()的值
  System.out.println(super);  // 编译报错，需要'.'一个方法
  ```

  当在子类对象中，子类想访问父类的东西，可以使用`super.`的方式访问。例如：方法覆盖后，子类内部虽然重写了父类的方法，但子类也想使用一下父类的被覆盖的方法，此时可以使用`super.`的方式。当子类中出现和父类一样的属性或者方法，此时，你要想去调用父类的那个属性或者方法，此时`super.`不能省略。

  - `this`和`super`都只能在对象内部使用。`this`代表当前对象本身，`super`代表当前对象的父类型特征。
  - `this.`是一个实例对象内部为了区分实例变量和局部变量。
  - `super.`是一个实例对象为了区分是子类的成员还是父类的成员。
  - **父类有，子类也有，子类想访问父类的，`super.`不能省略。**

- super()只能出现在构造方法的第一行

  通过当前的构造方法去调用“父类”中的对应的构造方法，目的是：创建子类对象时，先初始化父类型特征。用通俗的话来讲，要想有儿子，得先有父亲。

  我们来看下面代码：写两个类，Animal和Cat，Cat继承Animal。

  ```java
  //父类，Animal类
  class Animal {
  	//构造函数
  	public Animal() {
  		System.out.println("Animal类的无参数构造函数执行");
  	}
  }
  
  //子类，Cat类
  class Cat extends Animal{
  	//构造函数
  	public Cat() {
  		System.out.println("Cat类的无参数构造函数执行");
  	}
  }
  ```

  执行下面一行代码：

  ```java
  public static void main(String[] args) {
  	Cat cat = new Cat(); 
  }
  ```

  输出结果为：

  ```java
  Animal类的无参数构造函数执行
  Cat类的无参数构造函数执行
  ```

  我们发现实例化一个子类的对象，也就是调用了子类的构造方法，为什么父类的无参数构造方法也执行了，并在子类构造方法执行之前就已经执行了父类的无参数构造方法。我们上面提到`super()`和`this()`方法一样，都只能在构造方法的第一行出现。那么难道子类的构造方法第一行有一个隐形的`supper()`方法？

  答案是肯定的，我们在`Cat`的构造方法第一行加上一个`supper()`，发现执行结果不变。

  所以说**当子类的构造方法内第一行没有出现`super()`时，系统会默认给它加上无参数的"super()"方法**。

  同时系统默认加无参构造的`supper()`方法还有一个隐形的条件，那就是构造方法第一行不能有`this()`，因为这两个方法都只能出现在构造方法的第一行，所以我们得出一个结论：**构造方法中“this()”和“super()”不能同时出现，也就是“this()”和“super()”都只能出现在构造方法的第一行。**

  除了无参构造的`supper()`以外，我们也可以在构造方法的第一行使用有参数的`super(父类构造函数的参数列表)`，但值得注意的是，当子类构造方法执行有参数的`super(参数列表)`方法，你得确保父类中也有对应的有参数构造方法，不然会编译报错。同样的，当子类构造方法的第一行执行super()无参数方法，那么父类中一定要有无参数构造方法。

  这里要注意的是，类会默认加上无参构造方法，前提是你没有显式定义有参构造，如果你显式定义了有参构造，那么默认自动生成的无参构造方法就会失效，这是如果你不显式的定义无参构造函数，那么类就不再有无参构造方法。需要我们手动补充无参构造方法。

  无论你子类构造方法有没有`this()`和`super()`方法，实例化子类对象一定一定会执行对应的父类构造方法，即不管实例化了一个怎样的孩子，它一定会先实例化一个对应的父亲。

下面是一个实际的例子

```java
public class MyTest {
	
	public static void main(String[] args) {
		new Cat(); 
	}
}

//父类，Animal类
class Animal {
	//构造函数
	public Animal() {
		System.out.println("1：Animal类的无参数构造函数执行");
	}
	public Animal(int i) {
		System.out.println("2：Animal类的有int参数构造函数执行");
	}
}

//子类，Cat类
class Cat extends Animal{
	//构造函数
	public Cat() {
        // 这里实际上是调用Cat的有参构造方法this("")等价于Cat("")
		this("");
		System.out.println("3：Cat类的无参数构造函数执行");
	}
	public Cat(String str) {
		super(5);
		System.out.println("4：Cat类的有String参数构造函数执行");
	}
}
```

所以输出结果应该为：

```
2：Animal类的有int参数构造函数执行
4：Cat类的有String参数构造函数执行
3：Cat类的无参数构造函数执行
```

supper()主要使用场景为子类要调用父类的构造方法

## 总结

1. this和super一样，都是对象内部的引用变量，只能出现在对象内部；
2. this指向当前对象自己，super指向当前对象的父类型特征，故this的东西比super多，也就是super是this的一部分；
3. this()和super()都只能出现在构造方法的第一行，故this()和super()方法不能共存，当一个类的构造方法第一行中没有this()，也没有super()，系统默认有super()方法；
4. this()是构造方法中调用本类其他的构造方法，super()是当前对象构造方法中去调用自己父类的构造方法。