---
title: java开发规范-编程规约
categories: 计算机基础
tags:
- java
- 开发规范
---

## 编程规约

### 命名风格

1、（S）代码中的命名均不能以下划线或美元符号开始，也不能以下划线或美元符号结束。

2、代码中的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。

```
注：alibaba / taobao / youku / hangzhou 等国际通用的名称，可视同英文。
```

3、杜绝完全不规范的缩写，不要使用a、b、c等含义不清的变量或者使用lvl1/lvl2等根据数字区分的变量，避免望文不知义。为了达到代码自解释的目标，任何定义编程元素在命名时使用尽量完整单词组合来表达其意。

```
正例：从远程仓库拉取代码的类命名为PullCodeFromRemoteRepository
反例：AbstractClass“缩写”命名成AbsClass；变量int a的随意命名方式。
```

4、（S）包名统一使用小写，且用单数形式；类名如果有复数含义，可以使用复数形式。

```
应用工具类包名为com.alibaba.ai.util、类名为MessageUtils
```

5、（S）类名使用大驼峰命名法，但以下情形例外：DO / BO / DTO / VO / AO / PO等

```
正例：JavaServerlessPlatform / UserDO / XmlService
```

6、（S）接口和实现类的命名有两套规则：

1）【强制】对于Service和DAO类，基于SOA的理念，暴露出来的服务一定是接口，接口使用”I”开头，内部实现类去掉”I”前缀与接口区别。举例说明：

```
ICacheService CacheService
```

2）【推荐】如果是形容能力的接口名称，取对应的形容词为接口名（通常是–able的形容词）。举例说明：

```
AbstractTranslator实现Translatable接口
```

7、（S）抽象类命名使用Abstract开头；异常类命名使用Exception结尾；测试类命名以它要测试的类的名称开始，以Test结尾。

8、如果模块、接口、类、方法使用了设计模式，在命名时体现出具体模式，有利于阅读者快速理解架构设计理念

```java
public class OrderFactory;
public class LoginProxy;
public class ResourceObserver;
```

9、（S）方法名、参数名、成员变量、局部变量都统一使用小驼峰命名法。

10、方法命名时应该是动宾结构，动词部分标明该方法主要做的操作，名词部分标明要操作的类型。举例说明：

- 查询数据库使用queryAvailableData；
- 大表单多表组合保存使用saveAllData；
- 验证数据使用validatePostData；

11、接口类中的方法和属性不要加任何修饰符号（public 也不要加），并加上有效的Javadoc注释。尽量不要在接口里定义变量，如果一定要定义变量，肯定是与接口方法相关，并且是整个应用的基础常量。

```
说明：JDK8中接口允许有默认实现，那么这个default方法（建议子Abstract），是对所有实现类都有价值的默认实现。
```

12、各层命名规约（仅供参考）：

Service/DAO层方法命名规约

- 1）获取对象的方法用query/load/get作前缀，获取列表数据使用List结尾，获取数量使用Count结尾。
- 2）插入的方法用save/insert作前缀。
- 3）删除的方法用remove/delete作前缀。
- 4）修改的方法用update作前缀。

 领域模型命名规约

- 数据对象：xxxDO/Entity，xxx即为数据表名。
- 数据传输对象：xxxDTO，xxx为业务领域相关的名称。
- 展示对象：xxxVO，xxx一般为网页名称。
- POJO是DO/DTO/BO/VO的统称，禁止命名成xxxPOJO。

13、在常量与变量的命名时，表示类型的名词放在词尾，以提升辨识度：

```
正例：startTime / workQueue / nameList / TERMINATED_THREAD_COUNT
反例：startedAt / QueueOfWork / listName / COUNT_TERMINATED_THREAD
```

14、（S）常量命名全部大写，单词间用下划线隔开，要求能通过变量名看出具体含义

15、（S）类型与中括号紧挨相连来定义数组：

```java
正例：int[] arrayDemo。
反例：在main 参数中，使用String args[]来定义。
```

16、（S）避免在**子父类的成员变量**之间、或者**不同代码块的局部变量**之间采用完全相同的命名，使可读性降低。

17、枚举类名建议带上Enum后缀，枚举成员名称需要全大写，单词间用下划线隔开：

```
说明：枚举其实就是特殊的常量类，且构造方法被默认强制是私有。举例说明：
正例：枚举名字为ProcessStatusEnum的成员名称：SUCCESS / UNKNOWN_REASON。
```

18、代码和注释中都要避免使用任何语言的种族歧视性词语

19、（S）POJO类中的任何布尔类型的变量，都不要加is前缀，否则部分框架解析会引起序列化错误。

```
反例：定义为基本数据类型boolean isDeleted的属性，它的方法也是isDeleted()，（属性应该定义成deleted），框架在反向解析的时候，“误以为”对应的属性名称是deleted，导致属性获取不到，进而抛出异常。
```

20、注意方法内通篇paraMap和dataMap的情况，可个性化定义Map名称。变量名称应该见名知意，增加代码的可读性及可维护性

```
正例：userMap / searchJobMap / resultMap
```

### 常量定义

1、（S）不允许任何魔法值（即未经预先定义的常量）直接出现在代码中。如果只用一次，就不需要；多次复用的常量值，需要使用常量预先定义。举例说明：

```java
正例：
final String ID = "Id#taobao_";
String key = ID + tradeId;
反例：
String key = "Id#taobao_" + tradeId;
cache.put(key, value);
// 缓存get时，由于在代码复制时，漏掉下划线(双击只会选中单词)，导致缓存击穿(无法命中缓存)而出现问题
```

2、不要使用一个常量类维护所有常量，按常量功能进行归类，分出多个常量类，分开维护。

3、（S）常量的复用层次有五层：跨应用共享常量、应用内共享常量、子工程内共享常量、包内共享常量、类内共享常量。

```java
1） 跨应用共享常量：放置在平台库中，通常是tapn-public.jar中的tap目录下。
2） 应用内共享常量：放置在应用的public库中，通常是子模块中的前缀包目录下，如com.topscomm.cbo包下的CboSystemConst.java。
反例：易懂变量也要统一定义成应用内共享常量，两位工程师在两个类中分别定义了“YES”的变量： 类A 中： public static final String YES = "yes"; 类B 中： public static final String YES = "y"; A.YES.equals(B.YES)，预期是true，但实际返回为false，导致
线上问题。
3） 子工程内部共享常量：即在当前子工程的目录下。
4） 包内共享常量：即在当前包下根目录下。
5） 类内共享常量：直接在类内部private static final定义。
```

4、（S）long或者Long初始赋值时，使用大写的L，不能是小写的l，小写容易跟数字1混淆，造成误解。举例说明：

```
反例：Long a = 2l; 写的是数字的21，还是Long型的2。
```

5、如果变量值仅在一个固定范围内变化用enum类型来定义。如下季节的例子：

```java
public enum SeasonEnum {
    SPRING(1), SUMMER(2), AUTUMN(3), WINTER(4);
    private final int seasonId;
    SeasonEnum(int seasonId) {
        this.seasonId = seasonId;
    }
    public int getSeasonId() {
        return seasonId;
    }
}
```

### 代码格式

1、如果是大括号内为空，则简洁地写成{}即可，大括号中间无需换行和空格；如果是非空代码块则：

- 左大括号前不换行。
- 左大括号后换行。
- 右大括号前换行。
- 右大括号后还有else等代码则不换行；表示终止的右大括号后必须换行。

```java
if (integer == 8) {
    mutableBoolean.setFlag(false);
} else {
    stringBuilder.append(integer);
}
```

2、左小括号和字符之间不出现空格；同样，右小括号和字符之间也不出现空格；而左大括号前需要空格。详见第5条下方正例提示。

3、if/for/while/switch/do等保留字与括号之间都必须加空格。

4、任何二目、三目运算符的左右两边都需要加一个空格。

```java
说明：运算符包括赋值运算符=、逻辑运算符&&、加减乘除符号等。举例说明：
boolean isPass = "1".equals(verify) ? true : false;
```

5、采用4个空格缩进，禁止使用tab字符。如果使用tab缩进，必须设置1个tab为4个空格。IDEA设置tab为4个空格时，请勿勾选Use tab character；

![image-20230814201558697](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308142015799.png)

6、在进行类型强制转换时，右括号与强制转换值之间不需要任何空格隔，举例：

```java
long first = 1000000000000L;
int second = (int)first + 2;
```

7、单行字符数限制不超过**120个**，超出需要换行，换行时遵循如下原则：

- 1）第二行相对第一行缩进4个空格，从第三行开始，不再继续缩进，参考示例。
- 2）运算符与下文一起换行。
- 3）方法调用的点符号与下文一起换行。
- 4）方法调用中的多个参数需要换行时，在逗号后进行。
- 5）在括号前不要换行，见反例。

8、方法参数在定义和传入时，多个参数逗号后边必须加空格。举例说明：

```
method(args1, args2, args3);
```

9、IDE的text file encoding设置为UTF-8; IDE中文件的换行符使用Unix格式，不要使用Windows格式。

10、（S）单个方法的总行数不超过**80行**。

11、不同逻辑、不同语义、不同业务的代码之间插入一个空行分隔开来以提升可读性，注意是一行，不需要多行。

### OOP规范

1、避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可

2、（S）所有的覆写方法，必须加@Override注解。比如getObject()与get0bject()的问题。一个是字母的O，一个是数字的0，加@Override可以准确判断是否覆盖成功。

3、相同参数类型，相同业务含义，才可以使用Java的可变参数，避免使用Object。

```java
说明：可变参数必须放置在参数列表的最后，不推荐使用可变参数编程。举例说明：
正例：public List<User> listUsers(String type, Long... ids) {...}
```

4、（S）外部正在调用或者二方库依赖的接口，不允许修改方法签名，避免对接口调用方产生影响。接口或方法过时必须加@Deprecated注解，并清晰地说明采用的新接口或者新服务是什么。

5、（S）不能使用过时的类或方法。接口提供方既然明确是过时接口，那么有义务同时提供新的接口；作为调用方来说，有义务去考证过时方法的新实现是什么。

6、（S）Object的equals方法容易抛空指针异常，应使用常量或确定有值的对象来调用equals。举例说明：

```java
正例："test".equals(object);
反例：object.equals("test");
```

7、（S）所有整型包装类对象之间值的比较，全部使用equals方法比较。

8、（S）浮点数之间的等值判断，基本数据类型不能用==来比较，浮点数的包装数据类型不能用equals来判断。

9、定义数据对象DO/Entity类时，属性类型要与数据库字段类型相匹配。举例说明：

```
正例：数据库字段的bigint必须与类属性的Long类型相对应。
反例：某个案例的数据库表id字段定义类型bigint unsigned，实际类对象属性为Integer，随着id越来越大，超过Integer的表示范围而溢出成为负数。
```

10、（S）为了防止精度损失，禁止使用构造方法BigDecimal(double)的方式把double值转化为BigDecimal对象

```java
说明：BigDecimal(double)存在精度损失风险，在精确计算或值比较的场景中可能会导致业务逻辑异常。如： BigDecimal g = new BigDecimal(0.1f); 实际的存储值为：0.10000000149。

正例：优先推荐入参为String的构造方法，或使用BigDecimal的valueOf方法，此方法内部其实执行了Double的toString，而Double的toString按double的实际能表达的精度对尾数进行了截断。
BigDecimal recommend1 = new BigDecimal("0.1");
BigDecimal recommend2 = BigDecimal.valueOf(0.1);
```

11、BigDecimal的等值比较应使用compareTo()方法，而不是equals()方法。

```
说明：equals()方法会比较值和精度（1.0与1.00返回结果为false），而compareTo()则会忽略精度。
```

12、（S）关于基本数据类型与包装数据类型的使用标准如下：

1） **【强制】**RPC（Remote Procedure Call，远程过程调用协议）方法的返回值和参数必须使用包装数据类型。
2） **【推荐】**所有的局部变量使用基本数据类型。

说明：POJO类属性没有初值是提醒使用者在需要使用时，必须自己显式地进行赋值，任何NPE问题，或者入库检查，都由使用者来保证。举例说明：

```
正例：数据库的查询结果可能是null，因为自动拆箱，用基本数据类型接收有NPE风险。所以我们要使用包装数据类型作为返回值和参数。
反例：比如显示成交总额涨跌情况，即正负x%，x为基本数据类型，调用的RPC服务，调用不成功时，如果是基本数据类型，返回的是默认值，页面显示为0%，这是不合理的，应该显示成中划线。而包装数据类型的null值，能够表示额外的信息，如：远程调用失败，异常退出。
```

13、（S）定义DO/DTO/VO等POJO类时，不要设定任何属性默认值。举例说明：

```
反例：POJO类的createTime默认值为new Date()，但是这个属性在数据提取时并没有置入具体值，在更新其它字段时又附带更新了此字段，导致创建时间被修改成当前时间。
```

14、构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在init方法中。举例说明：

![image-20230814201612027](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308142016105.png)

15、禁止在POJO类中，同时存在对应属性xxx的isXxx()和getXxx()方法，统一使用getXxx()形式。

```java
说明：框架在调用属性xxx的提取方法时，并不能确定哪个方法一定是被优先调用到。
    boolean类型的变量用默认的getter生成器生成的是isXxx，而Boolean类型的变量则是getXxx。
    而如果命名定义为基本数据类型boolean isDeleted的属性，它的方法也是isDeleted()，（属性应该定义成deleted），框架在反向解析
    的时候，“误以为”对应的属性名称是deleted，导致属性获取不到，进而抛出异常。所以统一采用getXxx形式。
```

16、使用索引访问用String的split方法得到的数组时，需做最后一个分隔符后有无内容的检查，否则会有抛IndexOutOfBoundsException的风险。举例说明：

```java
String str = "a,b,c,,";
String[] ary = str.split(",");
// 预期大于3，结果是3，如果按照4来访问就有越界的风险
System.out.println(ary.length);
```

17、当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放置在一起，便于阅读；类内方法定义的顺序依次是：公有方法或保护方法> 私有方法> getter / setter 方法，第一句的规则高于第二句的规则。

```
说明：公有方法是类的调用者和维护者最关心的方法，首屏展示最好；保护方法虽然只是子类关心，也可能是“模板设计模式”下的核心方法；而私有方法外部一般不需要特别关心，是一个黑盒实现；因为承载的信息价值较低，所有Service和DAO的getter/setter方法放在类体最后。
```

19、（S）setter方法中，参数名称与类成员变量名称一致，this.成员名= 参数名。在getter/setter方法中，不要增加业务逻辑，增加排查问题的难度。

20、（S）循环体内，字符串的连接方式，使用StringBuilder的append方法进行扩展。

```java
说明：下例中，反编译出的字节码文件显示每次循环都会new出一个StringBuilder对象，然后进行append操作，最后通过toString方法返回String对象，造成内存资源浪费。

反例：
String str = "start";
for (int i = 0; i < 100; i++) {
	str = str + "hello";
}
```

21、final可以声明类、成员变量、方法、以及本地变量，下列情况使用final关键字：

```
1） 不允许被继承的类，如：String类。
2） 不允许修改引用的域对象。
3） 不允许被覆写的方法，如：POJO类的setter方法。
4） 不允许运行过程中重新赋值的局部变量。
5） 避免上下文重复使用一个变量，使用final可以强制重新定义一个变量，方便更好地进行重构。
```

22、（S）类成员与方法访问控制从严：

```
1） 如果不允许外部直接通过new来创建对象，那么构造方法必须是private。
2） 工具类不允许有public或default构造方法。
3） 类非static成员变量并且与子类共享，必须是protected。
4） 类非static成员变量并且仅在本类使用，必须是private。
5） 类static成员变量如果仅在本类使用，必须是private。
6） 若是static成员变量，考虑是否为final。
7） 类成员方法只供类内部调用，必须是private。
8） 类成员方法只对继承类公开，那么限制为protected。
```

```
说明：任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于模块解耦。
思考：如果是一个private的方法，想删除就删除，可是一个public的service成员方法或成员变量，删除一下，不得手心冒点汗吗？变量像自己的小孩，尽量在自己的视线内，变量作用域太大，无限制的到处跑，那么你会担心的。举例说明：
```

23、慎用Object的clone方法来拷贝对象。

```
说明：对象clone方法默认是浅拷贝，若想实现深拷贝需覆写clone方法实现域对象的深度遍历式拷贝。举例说明：
```

![image-20230814201653566](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308142016644.png)

24、序列化类新增属性时，请不要修改serialVersionUID字段，避免反序列失败；如果完全不兼容升级，避免反序列化混乱，那么请修改serialVersionUID值。

### 日期和时间

1、（S）日期格式化时，传入pattern中表示年份统一使用小写的y

```
说明：日期格式化时，yyyy表示当天所在的年，而大写的YYYY代表是week in which year（JDK7之后引入的概念），意思是当天所在的周属于的年份，一周从周日开始，周六结束，只要本周跨年，返回的YYYY就是下一年。
一般格式为yyyy-MM-dd HH-mm-ss
M代表月份，m代表分钟，H代表24小时制的小时，h代表12小时制，
```

2、（S）获取当前毫秒数：` System.currentTimeMillis()`; 而不是`new Date().getTime()`。

```java
// Date的实现  
public Date()
{
  this(System.currentTimeMillis());
}
也就是说，new Date()实际上就是调用了一下System.currentTimeMillis()，所以我们应该用后者来获取毫秒数，以防性能浪费。
    
说明：如果想获取更加精确的纳秒级时间值，使用System.nanoTime的方式。在JDK8中，针对统计时间等场景，推荐使用Instant类。
```

3、不允许在程序任何地方中使用：`java.sql.Date`、`java.sql.Time`、`java.sql.Timestamp`。

```
说明：第1个不记录时间，getHours()抛出异常；第2个不记录日期，getYear()抛出异常；第3个在构造方法super((time/1000)*1000)，在Timestamp 属性fastTime和nanos分别存储秒和纳秒信息。
反例：java.util.Date.after(Date)进行时间比较时，当入参是java.sql.Timestamp时，会触发JDK BUG(JDK9已修复)，可能导致比较时的意外结果。
```

4、不要在程序中写死一年为365天，避免在公历闰年时出现日期转换错误或程序逻辑错误，同样的，也要考虑闰年2月有29天的情况。闰年的2月份有29天，一年后的那一天不可能是2月29日。

```java
// 获取今年的天数
int daysOfThisYear = LocalDate.now().lengthOfYear();
// 获取指定某年的天数
LocalDate.of(2011, 1, 1).lengthOfYear();
```

5、使用枚举值来指代月份。如果使用数字，注意Date，Calendar等日期相关类的月份month取值在0-11之间。即0代表一月，11代表十二月。可以不使用数字，而是使用`Calendar.JANUARY、Calendar.FEBRUARY`等来指代月份。

### 集合处理

1、（S）关于hashCode和equals的处理，遵循如下规则：

```java
1） 只要覆写equals，就必须覆写hashCode。
2） 因为Set存储的是不重复的对象，依据hashCode和equals进行判断，所以Set存储的对象必须覆写这两个方法。
3） 如果自定义对象作为Map的键，那么必须覆写hashCode和equals。
说明：String已覆写hashCode和equals方法，所以我们可以愉快地使用String对象作为key来使用。
```

2、（S）ArrayList 的subList 结果不可强转成ArrayList ， 否则会抛出`ClassCastException`异常， 即`java.util.RandomAccessSubList cannot be cast to java.util.ArrayList`。

```java
说明：subList 返回的是ArrayList的内部类SubList，并不是ArrayList而是ArrayList的一个视图，对于SubList子列表的所有操作最终会反映到原列表上。
反例：
    List<String> list = new ArrayList<>();
	ArrayList newList = (ArrayList) list.subList(1,2);

// 下为SubList在ArrayList中的实现
SubList(AbstractList<E> parent,
        int offset, int fromIndex, int toIndex) {
    this.parent = parent;
    this.parentOffset = fromIndex;
    this.offset = offset + fromIndex;
    this.size = toIndex - fromIndex;
    this.modCount = ArrayList.this.modCount;
}
```

3、（S）在subList场景中，高度注意对原集合元素的增加或删除，均会导致子列表的遍历、增加、删除产生`ConcurrentModificationException`异常。举例说明：

```java
List<String> list = new ArrayList<>();
List<String> sList = list.subList(1, 5);
// 此处对原集合进行增加操作后，后续子列表sList的操作会报异常
list.add("1");
sList.remove(1);
```

4、使用Map的方法`keySet()/values()/entrySet()`返回集合对象时，不可以对其进行添加元素操作，否则会抛出`UnsupportedOperationException`异常。举例说明：

```java
反例：
    Map<String,Object> map = new HashMap<>();
	map.keySet().add("newKey");
```

5、Collections类返回的对象，如：`emptyList()/singletonList()`等都是`immutable list`，不可对其进行添加或者删除元素的操作。

```java
说明：如果查询无结果，会返回Conllections.emptyList()空集合对象，调用方一旦进行了添加元素的操作，就会触发`UnsupportedOperationException`异常。
而singletonList()则会返回一个仅包含指定对象o的不可变列表，如下，也是不可以添加或删除元素的。
public static <T> List<T> singletonList(T o)
```

6、（S）使用工具类Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法，它的add/remove/clear方法会抛出`UnsupportedOperationException`异常。

```java
说明：asList的返回对象是一个Arrays内部类，并没有实现集合的修改方法。Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组。举例说明：

String[] str = new String[] { "yang", "hao" };
List list = Arrays.asList(str);
list.add("yangguanbao");  // 第一种情况：运行时异常
str[0] = "changed";       // 第二种情况：list也会随之修改，反之亦然。
```

7、（S）使用集合转数组的方法，必须使用集合的toArray(T[] array)，传入的是类型完全一致、长度为0的空数组。

```java
说明：
反例：直接使用toArray无参方法存在问题，此方法返回值只能是Object[]类，若强转其它类型数组将出现ClassCastException错误。
正例：
    List<String> list = new ArrayList<>(2);
	list.add("guan");
	list.add("bao");
	String[] array = list.toArray(new String[0]);   
说明：使用toArray带参方法，数组空间大小的length：
1）等于0，动态创建与size相同的数组，性能最好。
2）大于0但小于size，会重新创建大小等于size的数组，增加GC负担。
3）等于size，在高并发情况下，数组创建完成之后，size正在变大的情况下，发生情况与上相同。
4）大于size，空间浪费，且在size处插入null值，存在NPE隐患。
```

8、在使用Collection接口任何实现类的addAll()方法时，都要对输入的集合参数进行NPE判断。

```java
addAll()第一行为`Object[] a = c.toArray();`，如果输入集合为null，那么会直接抛出异常
正例：
	List<String> list = new ArrayList<>();
	List<String> addList = null;
	if (addList != null){
		list.addAll(addList);
	}
```

9、（S）不要在foreach循环里进行元素的remove/add操作。remove元素请使用Iterator方式，如果并发操作，需要对Iterator对象加锁。

```java
正例：
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");
list.add("3");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    System.out.println(item);
    if ("3".equals(item)) {
        iterator.remove();
    }
}

反例：
for (String s : list) {
    System.out.println(s);
    // 删除元素大概率发生ConcurrentModificationException异常
    if ("2".equals(s)){
        list.remove(s);
    }
}
```

10、集合泛型定义时，在JDK7及以上，使用diamond语法或全省略。

```java
说明：菱形泛型，即diamond，直接使用<>来指代前边已经指定的类型。举例说明：
正例：
// diamond方式，即<>，new里不带类型
HashMap<String, String> userCache = new HashMap<>(16);
// 全省略方式，new不带尖括号
ArrayList<User> users = new ArrayList(10);
```

11、（S）集合初始化时，指定集合初始值大小。

```java
说明：HashMap使用HashMap(int initialCapacity) 初始化。
正例：initialCapacity = (需要存储的元素个数/ 负载因子) + 1。注意负载因子（即loader factor）默认为0.75，如果暂时无法确定初始值大小，请设置为16（即默认值）。
反例：HashMap需要放置1024个元素，由于没有设置容量初始大小，随着元素不断增加，容量被迫扩大7次（16*2^7），resize需要重建hash表，严重影响性能。
```

12、使用entrySet遍历Map类集合KV，而不是keySet方式进行遍历。

```java
说明：keySet其实是遍历了2次，一次是转为Iterator对象，另一次是从hashMap中取出key所对应的value。而entrySet只是遍历了一次就把key和value都放到了entry中，效率更高。如果是JDK8，使用Map.forEach方法。
values()返回的是V值集合，是一个list集合对象；keySet()返回的是K值集合，是一个Set集合对象；entrySet()返回的是K-V值组合集合。

HashMap<String, String> map = new HashMap<>();
HashMap<String, String> item = new HashMap<>();
// 易于理解的方式
map.entrySet().forEach(entry -> {
    if (entry.getKey().startsWith("used")) {
        item.put(entry.getKey(), entry.getValue());
    }
});
// 更加简洁的方式，表达含义与上述代码一致
map.forEach((key, value) -> {
    if (key.startsWith("used")) {
        item.put(key, value);
    }
});
```

13、高度注意Map类集合K/V能不能存储null值的情况，要分清那些Map的K/V可以为null，如下表格

| 集合类           | key          | value        | Super       | 说明                    |
| ---------------- | ------------ | ------------ | ----------- | ----------------------- |
| HashTable        | 不允许为null | 不允许为null | Dictionary  | 线程安全                |
| ConcurrenHashMap | 不允许为null | 不允许为null | AbstractMap | 锁分段技术（JDK8：CAS） |
| TreeMap          | 不允许为null | 允许为null   | AbstractMap | 线程不安全              |
| HashMap          | 允许为null   | 允许为null   | AbstractMap | 线程不安全              |

14、利用Set元素唯一的特性，可以快速对一个集合进行去重操作，避免使用List的contains方法进行遍历、对比、去重操作。举例说明：

```java
// 集合去重采用set方式
List<String> list = new ArrayList<>();
List<String> item = new ArrayList<>();
Set<String> set = new HashSet<>();
list.forEach(str -> {
    if (set.add(str)){
        item.add(str);
    }
});
```

15、判断所有集合内部的元素是否为空，使用isEmpty()方法，而不是size()==0的方法

```
说明：在某些集合中，前者的时间复杂度为O(1)，而且可读性更好。
```

16、（S）使用Map集合中“computeIfAbsent()”和“computeIfPresent()”方法时，不应用于添加null值。

```java
说明：“computeIfAbsent()”和“computeIfPresent()”方法当用于计算值的函数返回null时，entry key->null将不会添加到Map中。
例如：
// 需求：要向map中添加key为"B"，value为null的项
Map<String, Interger> map = new HashMap<>();
map.put("A", 1);
反例：
map.computeIfAbsent("B", value -> null); // 由于value为null，添加失败
正例：
if(!map.containsKey(key)){
	map.put("B", null);
}
```

### 并发处理



### 控制语句

1、（S）在一个switch块内，每个case要么通过continue/break/return等来终止，要么注释说明程序将继续执行到哪一个case为止；在一个switch块内，都必须包含一个default语句并且放在最后，即使它什么代码也没有。

```java
// 注意break是退出switch语句块，而return是退出方法体。
// switch后的括号大括号空格要求和if一致
switch (state) {
    case "1":
        // dosomething1
        break;
    case "2":
        // dosomething2
        break;
    default:
        // 非法状态，退出方法体
        return;
}
```

2、当switch括号内的变量类型为String并且此变量为外部参数时，必须先进行null判断，下面例子哪个分支都不会进入，会报空指针异常。

3、（S）在if/else/for/while/do语句中必须使用大括号，尽管只有一行代码。

4、在高并发场景中，避免使用”等于”判断作为中断或退出的条件

```
说明：如果并发控制没有处理好，容易产生等值判断被“击穿”的情况，使用大于或小于的区间判断条件来代替。
反例：判断剩余奖品数量等于0时，终止发放奖品，但因为并发处理错误导致奖品数量瞬间变成了负数，这样的话，活动无法终止。
```

5、表达异常的分支时，少用if-else方式，这种方式可以改写成：

```java
if (condition) {
...
return obj;
}
// 接着写else的业务逻辑代码;
if (elseCondition) {
    ...
}
```

说明：如果一定要使用if()...else if()...else...方式表达逻辑，为了避免后续代码维护困难，【强制要求】请勿超过3层。

```
正例：超过3层的if-else 的逻辑判断代码可以使用卫语句、策略模式、状态模式等来实现，其中卫语句即代码逻辑先考虑失败、异常、中断、退出等直接返回的情况，以方法多个出口的方式，解决代码中判断分支嵌套的问题，这是逆向思维的体现。
```

6、（S）除常用方法（如getXxx/isXxx）等外，不要在条件判断中执行其它复杂的语句，将复杂逻辑判断的结果赋值给一个有意义的布尔变量名，以提高可读性。

```
说明：很多if 语句内的逻辑表达式相当复杂，与、或、取反混合运算，甚至各种方法纵深调用，理解成本非常高。如果赋值一个非常好理解的布尔变量名字，则是件令人爽心悦目的事情。
```

```java
正例：
// 伪代码如下
final boolean existed = (file.open(fileName, "w") != null) && (...) || (...);
if (existed) {
	...
}
反例：
if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) {
	selfInterrupt();
}
```

7、不要在其它表达式（尤其是条件表达式）中，插入赋值语句。赋值语句需要清晰地单独成为一行。

8、循环体中的语句要考量性能，以下操作尽量移至循环体外处理，如定义对象、变量、获取数据库连接，进行不必要的try-catch操作（思考这个try-catch是否可以移至循环体外）

9、（S）避免采用取反逻辑运算，因为取反逻辑一定存在其对应的正向逻辑写法。

10、公开接口需要进行入参保护，尤其是批量操作的接口。

```
反例：接口文档中，标明传递参数userCode，代表用户编号，但是接口内部逻辑没有做任何验证。之后调用方在userCode中传递了userId，导致接口报错。
```

11、下列情形，需要进行参数校验：

- 1） 调用频次低的方法。
- 2） 执行时间开销很大的方法。此情形中，参数校验时间几乎可以忽略不计，但如果因为参数错误导致中间执行回退，或者错误，那得不偿失。
- 3） 需要极高稳定性和可用性的方法。
- 4） 对外提供的开放接口，不管是RPC/API/HTTP接口。
- 5） 敏感权限入口。

下列情形，不需要进行参数校验：

- 1） 极有可能被循环调用的方法。但在方法说明里必须注明外部参数检查要求。
- 2） 底层调用频度比较高的方法。毕竟是像纯净水过滤的最后一道，参数错误不太可能到底层才会暴露问题。一般DAO层与Service层都在同一个应用中，部署在同一台服务器中，所以DAO的参数校验，可以省略。
- 3） 被声明成private只会被自己代码所调用的方法，如果能够确定调用方法的代码传入参数已经做过检查或者肯定不会有问题，此时可以不校验参数。

12、三目运算符condition ?  表达式1  :  表达式2中，高度注意表达式1和2在类型对齐时，可能抛出因自动拆箱导致的NPE异常。

```
说明：以下两种场景会触发类型对齐的拆箱操作： 1） 表达式1或表达式2的值只要有一个是原始类型，也就是基本数据类型。2） 表达式1或表达式2的值的类型不一致，会强制拆箱升级成表示范围更大的那个类型(比如int转变为long)。
```

```java
反例：
Integer a = 1;
Integer b = 2;
Integer c = null;
Boolean flag = false;
// a*b的结果是int类型，那么c会强制拆箱成int类型，抛出NPE异常
Integer result=(flag ? a*b : c);
```

14、（S）程序中不允许出现不会结束的循环。比如for( ; ; )、while(true)等等

### 注释规约

1、（S）类、类属性、类方法的注释必须使用Javadoc规范，使用`/**内容*/`格式，不得使用// xxx方式。其中类、类方法必须添加创建者与创建日期。

```
说明：在IDE编辑窗口中，Javadoc方式会提示相关注释，生成Javadoc可以正确输出相应注释；在IDE中，工程调用方法时，不进入方法即可悬浮提示方法、参数、返回值的意义，提高阅读效率。如下图
```

2、（S）所有的抽象方法（包括接口中的方法）必须要用Javadoc注释、除了返回值、参数、异常说明外，还必须指出该方法做什么事情，实现什么功能。并且对子类的实现要求，或者调用注意事项，也应一并说明。

3、（S）方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释使用/* */注释，注意与代码对齐。

4、（S）所有的枚举类型字段必须要有注释，说明每个数据项的用途。

5、与其“半吊子”英文来注释，不如用中文注释把问题说清楚。专有名词与关键字保持英文原文即可。

```
反例：“TCP连接超时”解释成“传输控制协议连接超时”，理解反而费脑筋。
```

6、代码修改的同时，注释也要进行相应的修改，尤其是参数、返回值、异常、核心逻辑等的修改。

```
说明：代码与注释更新不同步，就像路网与导航软件更新不同步一样，如果导航软件严重滞后，就失去了导航的意义。
```

7、（S）谨慎注释掉代码。在上方详细说明，而不是简单地注释掉。如果无用，则删除。

```
说明1：对于垃圾代码或过时配置，坚决清理干净，避免程序过度臃肿，代码冗余。
说明2：代码被注释掉有两种可能性：
1）后续会恢复此段代码逻辑。
2）永久不用。
前者如果没有备注信息，难以知晓注释动机。后者建议直接删掉（代码仓库已然保存了历史代码）。
```

8、对于注释的要求：

- 1、能够准确反映设计思想和代码逻辑；
- 2、能够描述业务含义，使别的程序员能够迅速了解到代码背后的信息。完全没有注释的大段代码对于阅读者形同天书，注释是给自己看的，即使隔很长时间，也能清晰理解当时的思路；注释也是给继任者看的，使其能够快速接替自己的工作。
- 3、注释内容，一般不加序号，防止后续添加其他逻辑，导致序号变更。

9、好的命名、代码结构是自解释的，注释力求精简准确、表达到位。避免出现注释的一个极端：过多过滥的注释，代码的逻辑一旦修改，修改注释是相当大的负担。

10、（S）特殊注释标记，请注明标记人与标记时间。注意及时处理这些标记，通过标记扫描，经常清理此类标记。线上故障有时候就是来源于这些标记处的代码。

- 1） 待办事宜（TODO）:（标记人，标记时间，[预计处理时间]） 表示需要实现，但目前还未实现的功能。这实际上是一个Javadoc的标签，目前的Javadoc还没有实现，但已经被广泛使用。只能应用于类，接口和方法（因为它是一个Javadoc标签）。
- 2） 错误，不能工作（FIXME）:（标记人，标记时间，[预计处理时间]） 在注释中用FIXME标记某代码是错误的，而且不能工作，需要及时纠正的情况。

11、对逻辑中的特殊处理，其他人可能不明确或在特殊场景下才会触发的代码加上注释。

```
SQL中加入1=1目的是为了避免走mybatis缓存（因为前后执行了两遍相同查询，中间还做过更新）。
```

### 平台规约

1、平台代码生成工具生成的Auto类（ControllerAuto和ServiceAuto）不要去修改，有特殊需求的可在对应的Controller类和Service类中覆写。

2、Service层before/after前缀开头的方法，应该是在Service层中某个业务的前后调用，不建议Service层的公开方法命名为before/after开头。

3、Controller层调用Service层方法时能在一个事务中处理完成的，就一次调用，不要多次调用，保证事务一致。

4、重写平台方法时，正常情况下第一行代码通过super先调用父类的同名方法，哪怕当前版本下平台方法的实现为空。

5、业务层方法不要与平台的方法重名，可以重新定义方法名。

```
业务类中单据提交的submit方法，若不是提交审批流操作，则可以修改为submitBill，与平台的submit方法区分开。
```

6、考虑到微服务拓展，Controller层推荐也定义出接口层，通过openFeign进行接口调用时减少冗余代码。

7、常见的实体类表名、字段名，平台自动生成的Entity中已经定义好，在代码中使用这些常量进行编码，防止表名、字段发生变化时，增加代码排查难度。

8、不要在循环中单次调用update/insert方法，使用批量更新/批量插入的方式。

```
说明：在循环中可以取出要更新的集合，然后再集合外面进行批量更新。
```

9、正常情况下，禁止在daoImpl中写代码，因为service中的方法支持直接调用xml中的方法。

10、重写平台或者spring 的配置类(@Configuration 配置类) ， 统一放到运行项目的com.topscomm.main.config包下。

11、Response<Result> 用于接口与前台交互，不要写在Service中，service中一般不需要加Try,catch,除非用记录日志之类的，如果方法出现异常但不抛异常，那么不会触发事务回滚。Service 的方法中， 添加Rollbackfor = Exception.class 时， Rollbackfor 尽量使用Exception，捕获所有异常，否则会出现没捕获的异常类型导致事务回滚失败。

12、平台的updateFields 和updateFieldsBatch 方法， 会自动修改modifiedon 字段， 在updateFieldList中无需添加此字段。insert单个对象会追加id，createon，createuserid等字段，但是insertBath不会添加createuserid字段，需要自行赋值。

13、使用系统参数缓存类ParameterCache和配置文件参数类ConfigCache获取某个参数值时，尽量传递第二个参数，增加默认值，防止在没有设置相关参数时出现null的情况。

14、平台Mybatis的xml中返回的Map或List<Map>结果，均使用平台的BasicLowerMap类，该类会将Map中的所有key全部转小写，即会将sql语句中select结果的所有字段转小写，但是若不是返回的平台BasicLowerMap类，则不会做此转换，返回的Map会与sql语句中select的列名一致。

```xml
<select id = "queryMapByWhere" resultType="com.topscomm.tap.basic.core.BasicLowerMap">
</selcet>
```

### 前后端规约

1、前后端交互的API，需要明确协议、域名、路径、请求方法、请求内容、状态码、响应体。

```
1） 协议：如果条件允许，生产环境尽量使用HTTPS，HTTP的接口需要做数据安全考虑。
2） 路径：每一个API需对应一个路径，表示API具体的请求地址：
	a） 代表一种资源，只能为名词，推荐使用复数，不能为动词，请求方法已经表达动作意义。
	b） URL路径开头使用小写，多个单词如果需要分隔，使用驼峰命名。
	c） 路径禁止携带表示请求内容类型的后缀，比如".json",".xml"，通过accept头表达即可。
3） 请求方法：对具体操作的定义，常见的请求方法如下：
	a） GET：从服务器取出资源。
	b） POST：在服务器新建一个资源。
	c） PUT：在服务器更新资源。
	d） DELETE：从服务器删除资源。
4） 请求内容：URL带的参数必须无敏感信息或符合安全要求；body里带参数时必须设置Content-Type。
5） 响应体：响应体body可放置多种数据类型，由Content-Type头来确定。
```

2、前后端数据列表相关的接口返回，如果为空，则返回空数组[]或空集合{}。

```java
说明：此条约定有利于数据层面上的协作更加高效，减少前端很多琐碎的null判断。举例说明：
if (list == null) {
	return ResponseResult.ok(new ArrayList<>());
} else {
	return ResponseResult.ok(list);
}
```

3、服务端发生错误时，返回给前端的响应信息必须包含HTTP状态码，errorCode、message三个部分。

```javascript
说明：errorCode中，要根据状态码指示前台，该异常信息(message)是否可以允许前台提示给用户。举例说明：
{
	"errorCode":"success",
	"message":"操作成功",
	"result":false,
	"statusCode":200,
	"success":true,
	"timestamp":1652661993376
}
```

4、在前后端交互的JSON格式数据中，所有的key必须为小写字母开始的lowerCamelCase风格，符合英文表达习惯，且表意完整。涉及数据库实体类对应的，可以不满足该要求。

```java
正例：errorCode / errorMessage / assetStatus / menuList / orderList / configFlag
反例： ERRORCODE / ERROR_CODE / error_message / error-message / errormessage / ErrorMessage / msg
```

5、对于需要使用超大整数的场景，服务端一律使用String字符串类型返回，禁止使用Long类型

```
说明：Java服务端如果直接返回Long整型数据给前端，JS会自动转换为Number类型（注：此类型为双精度浮点数，表示原理与取值范围等同于Java中的Double）。Long类型能表示的最大值是2的63次方-1，在取值范围之内，超过2的53次方(9007199254740992)的数值转化为JS的Number时，有些数值会有精度损失。

扩展说明，在Long取值范围内，任何2的指数次整数都是绝对不会存在精度损失的，所以说精度损失是一个概率问题。若浮点数尾数位与指数位空间不限，则可以精确表示任何整数，但很不幸，双精度浮点数的尾数位只有52位。
```

6、HTTP请求通过URL传递参数时，不能超过2048字节

```
说明：不同浏览器对于URL的最大长度限制略有不同，并且对超出最大长度的处理逻辑也有差异，2048字节是取所有浏览器的最小值。

反例：某业务将退货的商品id列表放在URL中作为参数传递，当一次退货商品数量过多时，URL参数超长，传递到后端的参数被截断，导致部分商品未能正确退货。
```

7、HTTP请求通过body传递内容时，必须控制长度，超出最大长度后，后端解析会出错

```
说明：nginx默认限制是1MB，tomcat默认限制为2MB，当确实有业务需要传较大内容时，可以通过调大服务器端的限制。举例说明：
```

8、在翻页场景中，用户输入参数的小于1，则前端返回第一页参数给后端；后端发现用户输入的参数大于总页数，直接返回最后一页。

```java
if (query.getCurrentPage() < 1) {
	query.setCurrentPage(1);
} else if (query.getCurrentPage() > totalPage) {
	query.setCurrentPage(totalPage);
}
```

9、服务端返回的数据，使用JSON格式而非XML

```
说明：尽管HTTP支持使用不同的输出格式，例如纯文本，JSON，CSV，XML，RSS甚至HTML。如果我们使用的面向用户的服务，应该选择JSON作为通信中使用的标准数据交换格式，包括请求和响应。此外，application/JSON是一种通用的MIME类型，具有实用、精简、易读的特点。
```

10、前后端的时间格式统一为"yyyy-MM-dd HH:mm:ss"，统一为GMT

```java
fastJsonConfig.setDateFormat("yyyy-MM-dd HH:mm:ss");
fastConverter.setFastJsonConfig(fastJsonConfig);
return fastConverter;
```

### 编程规约

1、（S）在使用正则表达式时，Pattern要定义为static final静态变量，利用好其预编译功能，以避免执行多次预编译。可以有效加快正则匹配速度。

```JAVA
【错误用法】
// 没有使用预编译
private void func(...) {
	if (Pattern.matches(regexRule, content)) {
		...
	}
}

// 多次预编译
private void func(...) {
	Pattern pattern = Pattern.compile(regexRule);
	Matcher m = pattern.matcher(content);
	if (m.matches()) {
		...
	}
}

// 正确用法：定义为静态变量
private static final Pattern pattern = Pattern.compile(regexRule);

private void func(...) {
	Matcher m = pattern.matcher(content);
	if (m.matches()) {
		...
	}
}
```

2、（S）注意Math.random() 这个方法返回是double类型，注意取值的范围0≤x<1（能够取到零值，注意除零异常），如果想获取整数类型的随机数，不要将x放大10的若干倍然后取整，直接使用Random对象的`nextInt`或者`nextLong`方法。

3、日期格式化时，传入pattern中表示年份统一使用小写的y。

```
说明：日期格式化时，yyyy表示当天所在的年，而大写的YYYY代表是week in which year（JDK7之后引入的概念），意思是当天所在的周属于的年份，一周从周日开始，周六结束，只要本周跨年，返回的YYYY就是下一年。另外需要注意m、M、H、h的区别
```

4、不要在视图模板中加入任何复杂的逻辑。(不要在JSP中写java代码)

```
说明：根据MVC理论，视图的职责是展示，不要抢模型和控制器的活。
```

5、多接口的同一个方法不加default，当同一个类实现多个接口时，而这些接口之间没有继承关系，且都有同一个方法时，多个接口之间的default实现会强制类实现该方法，否则报错。

```
若interface1有方法save()，带default实现；interface2有方法save()，带default实现；
类CboUserService实现两个接口，而save方法由于在两个接口中都有默认实现，若CboUserService不重写save方法，则报错。
```

6、参数校验应该放在查询数据库之前进行。一般情况下，在方法最开始进行参数校验，防止由于参数错误，造成额外的资源消耗。

7、代码中尽量不出现警告，在eclipse或idea，在警告位置都会有代码修改提示，按照代码提示进行修改，消除警告。

8、简单的for循环语句，尽量改为stream()的写法，提高代码可读性。但stream中括号层数不要太多，可读性太差。

9、存在级联删除的场景，如删除主表时级联删除子表，可以采用平台的引用控制功能，或者重写deleteBefore方法。重写时，不要使用替换某个字段的方式，而是使用子查询的方式进行。

```java
// 不要使用替换某个字段的方式
String pickListLineSql = whereSql.replace("id", "picklistid");

// 正确的应该重写deleteBefore方法，并且采用子查询的方式，
protected void deleteBefore(String whereSql){
    this.mesMOPickOrderLineService.deleteByWhere("mopickorderid in (select id from" + MesMOPickOrderEntity.tableName + "where" + whereSql + ")")
}
```

10、对于一屏幕都显示不开的方法，必须要重构。根据代码逻辑，将进行某一类操作的代码单独提取为一个方法，使得逻辑清晰。

11、枚举中的数据类型，正常要与数据库字段类型一致，减少使用时的类型转换次数。如代码中，数据库字段类型为int，但枚举值是string，导致每次使用都需要做类型转换。

12、public方法中有对数据库新增修改删除的操作，方法上要加事务控制。

```
即使是内部只有一次数据库操作，也应该加上，因为其为public方法，无法保证后续是否被其他方法调用。
```

13、拼接SQL语句时，前后尽量都添加空格，防止在拼接过程中，出现上一个SQL的结束和下一个SQL开始位置没有空格，造成单词合并的问题。并且为了避免参数值为空时发生的SQL执行错误，SQL拼接参数时应添加单引号。

![image-20230814201934348](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308142019428.png)

14、编码时经常用到的SQL拼接where条件，减少不必要的1=1的出现。

```
说明：1=1是在不确定后续是否有sql，或者后续的sql无法确定哪个是第一条时（因为第一个不需要加and），才需要增加的。
```

15、XML中的SQL语句，嵌套查询需要进行缩进，表名的别名定义，使用表名的简写，便于阅读。

![image-20230814201942923](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308142019048.png)

16、（S）@SpringBootApplication和@ComponentScan不应在默认包中使用。

```
说明：ComponentScan配置了扫描哪些包下的文件加载到Springbean容器中，@ComponentScan做的事情就是告诉Spring从哪里找到bean。如果不配置的话，则默认扫描配置了SpringBootApplication类所在包及其子级包下的所有文件。
所以，如果配置了SpringBootApplication类在默认包（相当于根路径）下，且没有配置ComponentScan，则会扫描根路径下的所有文件，这将减慢应用程序的启动速度，或者造成程序无法正常启动；如果ComponentScan明确配置了默包，也会导致扫描根路径下的所有文件。
```

17、（S）方法不应该直接调用本类中添加aop注解的方法。

```java
说明：在一个SpingBean中，如果直接调用带有注解的增强方法，其实是调用的本类的方法，未调用代理类中的增强方法，此时aop注解会失效。可以从Spring容器中获取bean，然后调用方法，这时候就得到了增强方法。

反例：
@Override
public void doTheThing() {
	// ...
	// 调用本类的方法，不会触发actuallyDoTheThing方法的Transactional注解
	actuallyDoTheThing();
}

@Override
@Transactional
public void actuallyDoTheThing() {
	// ...
}


正例：
@Override
public void doTheThing() {
	// 通过从容器中获取bean的方式调用方法，此时就能得到增强方法。
	ICboJobService service = (ICboJobService) SpringUtil.getBean("cboJobService");
	service.actuallyDoTheThing();
}
@Override
@Transactional
public void actuallyDoTheThing() {
	// ...
}
```

18、（S）Apache BeanUtils性能较差，可以使用其他方案比如MapStruct，Spring  BeanUtils，Cglib BeanCopier。

19、（S）不能以追加写入的方式将ObjectOutputStream写入文件。

说明：ObjectOutputStream在构造时，首先会将流的头部信息（“AC ED 00 05”）写入到文件中；以追加方式写入文件，会造成头部信息无法解析。

```java
FileOutputStream fos;
ObjectOutputStream oos;

fos = new FileOutputStream(filename);
oos = new ObjectOutputStream(fos);
out.writeObject("a");
out.flush();
out.close();

// 这里第二个参数true表示追加方式写入，会造成头部信息无法解析，应该为fos = new FileOutputStream(filename);
fos = new FileOutputStream(filename, true);
oos = new ObjectOutputStream(fos);
out.writeObject("b");
out.flush();
out.close();
```

20、（S）正确使用printf 样式的格式字符串。

```java
反例：
// %d对应的是整数类型（十进制），而不是字符串
String.format("The value of my integer is %d", "Hello World");
正例：
String.format("The value of my integer is %d", 6);
```

21、（S）正则表达式应该合格有效。

说明：正则表达式是以边界符号"^"开头，以"$"结尾。如果意外交换"^" 和"$"，那么该表达式则永远无法匹配。

```java
反例：
// This can never match because $ and ^ have been switched around
Pattern.compile("$[a-z]+^"); // Noncompliant
正例：
Pattern.compile("^[a-z]+$");
```

22、（S）"=+"不能代替"+="

说明：在JAVA中，例如+=, -= 或!=会为一个运算符将编译并运行，但是像=+，=-或=！之间没有空格被使用时则会导致规则问题，所以=之后至少有一个空白字符。

23、（S）“Random”对象应该被重用。

说明：每次需要一个随机值时都创建一个新的随机对象是低效的，并且根据JDK的不同可能产生的数字不是随机的。为了提高效率和随机性，请创建一个Random，然后存储并重用它。推荐使用Java中的SecureRandom，SecureRandom提供了比Random更安全和更强的随机数生成器（RNG）。

24、（S）安全随机数种子不应该是可预测的。

说明：java.security.SecureRandom 类提供了一个适用于密码学的强大随机数生成器(RNG)。然而， 用一个常量或另一个可预测的值作为种子会显着削弱它。一般来说， 依靠SecureRandom 实现提供的种子要安全得多。

当使用以下任一种子调用SecureRandom.setSeed() 或SecureRandom(byte[]) 时，此规则会引发问题：

- 常数
- 系统时间

```java
反例：
SecureRandom sr = new SecureRandom();
sr.setSeed(123456L); // Noncompliant
int v = sr.next(32);
sr = new SecureRandom("abcdefghijklmnop".getBytes("us-ascii")); // Noncompliant
v = sr.next(32);
正例：
SecureRandom sr = new SecureRandom();
int v = sr.next(32);
```

25、（S）不要使用finalize()命名方法。

说明： 垃圾回收器在对象未被引用后的某个时刻调用Object.finalize()。通常，重载Object.finalize()是个坏主意，因为：

- 垃圾回收器可能不会调用重载。
- 用户不应该调用Object.finalize()，这会导致混淆。

除此之外，如果一个方法实际上没有覆盖Object.finalize()，那么将其命名为“finalize”是一个糟糕的想法。

```java
反例：
public int finalize(int someParameter) {
	/* ... */
}
```

26、（S）"super.finalize()"应该在"Object.finalize()" 方法的最后调用。

说明：在Java中，当一个对象不再被引用时，它会被垃圾回收器自动回收。在垃圾回收之前，对象的finalize()方法会被调用，以便在对象被销毁之前执行一些必要的清理操作，例如释放一些系统资源。

当我们重写finalize()方法时，如果有一些父类也需要进行清理操作，那么我们应该在自己的实现中调用父类的finalize()方法。如果我们在子类中最后调用super.finalize()，可以确保在子类中定义的清理操作完成后再进行父类的清理操作。如果我们在子类中先调用super.finalize()，那么父类中的清理操作可能会在子类的清理操作之前执行，而此时子类还可能在使用父类中的资源，这可能会导致错误或未定义的行为。因此，为了确保正确的执行顺序，应该在子类的finalize()方法中最后调用super.finalize()。

```java
反例：
protected void finalize() { // Noncompliant; no call to super.finalize();
	releaseSomeResources();
}

protected void finalize() {
	super.finalize(); // Noncompliant; this call should come last
	releaseSomeResources();
}

正例：
protected void finalize() {
	releaseSomeResources();
	super.finalize();
}
```

27、（S）不应使用不安全的临时文件创建方法。

说明：使用File.createTempFile 作为创建临时目录的第一步会导致竞争条件，并且本质上是不可靠和不安全的。相反，应该使用Files.createTempDirectory (Java 7+)。

当立即执行以下步骤时，此规则会引发问题：

```java
call to File.createTempFile
delete resulting file
call mkdir on the File object
反例：
File tempDir;
tempDir = File.createTempFile("", ".");
tempDir.delete();
tempDir.mkdir(); // Noncompliant
正例：
Path tempPath = Files.createTempDirectory("");
File tempDir = tempPath.toFile();
```

28、（S）用HttpSecurity 方法配置url 应该按照一定的声明顺序。

说明：在HttpSecurity.authorizeRequests() 方法上配置的URL 模式会按照它们声明的顺序考虑。

```java
反例：
protected void configure(HttpSecurity http) throws Exception {
	http.authorizeRequests()
		.antMatchers("/resources/**", "/signup", "/about").permitAll() // Compliant
		.antMatchers("/admin/**").hasRole("ADMIN")
		// Noncompliant; the pattern "/admin/login" should appear before "/admin/**"
		.antMatchers("/admin/login").permitAll()
		.antMatchers("/**", "/home").permitAll()
		// Noncompliant; the pattern "/db/**" should occurs before "/**"
		.antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')");
}
```

```java
正例：
protected void configure(HttpSecurity http) throws Exception {
	http.authorizeRequests()
		// Compliant
		.antMatchers("/resources/**", "/signup", "/about").permitAll()
		.antMatchers("/admin/login").permitAll()
		.antMatchers("/admin/**").hasRole("ADMIN") // Compliant
		.antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')")
		// Compliant; "/**" is the last one
		.antMatchers("/**", "/home").permitAll()
		.and().formLogin().loginPage("/login").permitAll();
}
```

29、（S）应使用有效索引调用“PreparedStatement”和“ResultSet”方法。

```java
反例：
PreparedStatement ps = con.prepareStatement(
    "SELECT fname, lname FROM employees where hireDate > ? and salary < ?");
	ps.setDate(0, date); // 不正确，索引从1开始
	ps.setDouble(3, salary); // 不正确，只有两个参数，没有第三个
	ResultSet rs = ps.executeQuery();
	while (rs.next()) {
		String fname = rs.getString(0); // 不正确，索引从1开始
		// ...
	}
```

```java
正例：
PreparedStatement ps = con.prepareStatement(
    "SELECT fname, lname FROM employees where hireDate > ? and salary < ?");
	ps.setDate(1, date);
	ps.setDouble(2, salary);
	ResultSet rs = ps.executeQuery();
	while (rs.next()) {
		String fname = rs.getString(1);
		// ...
	}
```



