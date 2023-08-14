---
title: java开发规范-异常日志
categories: 计算机基础
tags:
- java
- 开发规范
---

## 异常日志

### 异常处理

1、Java 类库中定义的可以通过预检查方式规避的RuntimeException异常不应该通过catch 的方式来处理，`NullPointerException`，`IndexOutOfBoundsException`等等。

```
说明：无法通过预检查的异常除外，比如，在解析字符串形式的数字时，可能存在数字格式错误，不得不通过catch NumberFormatException来实现。
```

2、异常捕获后（也就是在try-catch里）不要用来做流程控制，条件控制。

```
说明：异常设计的初衷是解决程序运行中的各种意外情况，且异常的处理效率比条件判断方式要低很多。
```

3、catch时请分清稳定代码和非稳定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的catch尽可能进行区分异常类型，再做对应的异常处理。

```
说明：对大段代码进行try-catch，使程序无法根据不同的异常做出正确的应激反应，也不利于定位问题，这是一种不负责任的表现。

正例：用户注册的场景中，如果用户输入非法字符，或用户名称已存在，或用户输入密码过于简单，在程序上作出分门别类的判断，并提示给用户。
```

4、捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的内容。

5、（S）事务场景中，抛出异常被catch后，如果需要回滚，一定要注意回滚事务。

6、（S）finally块必须对资源对象、流对象进行关闭，有异常也要做try-catch。同时，不要在finally块中使用return。

```
说明：如果JDK7及以上，可以使用try-with-resources方式。
并且finally块就是无论如何都会执行到的代码块，try块中的return语句执行成功后，并不马上返回，而是继续执行finally块中的语句，如果此处存在return语句，则在此直接返回，无情丢弃掉try块中的返回点。
```

```java
private int x = 0;
public int checkReturn() {
	try {
		// x等于1，此处不返回
		return ++x;
	} finally {
		// 返回的结果是2
		return ++x;
	}
}
```

7、捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类，也就是不能存在接不住的情况。

8、在调用RPC、二方包、或动态生成类的相关方法时，捕捉异常必须使用Throwable类来进行拦截。

```
说明：通过反射机制来调用方法，如果找不到方法，抛出NoSuchMethodException。什么情况会抛出
NoSuchMethodError呢？二方包在类冲突时，仲裁机制可能导致引入非预期的版本使类的方法签名不匹配，
或者在字节码修改框架（比如：ASM）动态创建或修改类时，修改了相应的方法签名。这些情况， 
即使代码编译期是正确的， 但在代码运行期时， 会抛出NoSuchMethodError。
```

9、方法的返回值可以为null，不强制返回空集合，或者空对象等，必须添加注释充分说明什么情况下会返回null值。

```
说明：本手册明确防止NPE是调用者的责任。即使被调用方法返回空集合或者空对象，对调用者来说，
也并非高枕无忧，必须考虑到远程调用失败、序列化失败、运行时异常等场景返回null的情况。
```

10、（S）防止NPE，是程序员的基本修养，注意NPE产生的场景：

- 返回类型为基本数据类型，return包装数据类型的对象时，自动拆箱有可能产生NPE。

  反例：public int f() { return Integer对象}， 如果为null，自动解箱抛NPE。

- 数据库的查询结果可能为null。

- 集合里的元素即使isNotEmpty，取出的数据元素也可能为null。

- 远程调用返回对象时，一律要求进行空指针判断，防止NPE。

- 对于Session中获取的数据，建议进行NPE检查，避免空指针。

- 级联调用obj.getA().getB().getC()；一连串调用，易产生NPE。

说明：使用JDK8的Optional类来防止NPE问题。

11、定义时区分unchecked / checked 异常，避免直接抛出new RuntimeException()，更不允许抛出Exception或者Throwable，应使用有业务含义的自定义异常。推荐业界已定义过的自定义异常，如：DAOException / ServiceException等。举例说明：

12、对于公司外的http/api开放接口必须使用errorCode；而应用内部推荐异常抛出；跨应用间RPC调用优先考虑使用Result方式，封装success()方法、errorCode、message；而应用内部直接抛出异常即可。

说明：关于RPC方法返回方式使用Result方式的理由：

- 使用抛异常返回方式，调用方如果没有捕获到就会产生运行时错误。
- 如果不加栈信息，只是new自定义异常，加入自己的理解的error message，对于调用端解决问题的帮助不会太多。如果加了栈信息，在频繁调用出错的情况下，数据序列化和传输的性能损耗也是问题。

13、把用户可以维护的信息，直接写进返回的错误信息中，不要使用通用的返回信息，否则用户不知道是因为本身操作失误还是系统原因。也就是把详细的错误信息展示出来，比如用户输入错误，这是一个通用的返回信息，而我们加上账号应该为xxxxx形式，这样用户就能很清晰的认识到错误的原因。

14、在同一代码逻辑里抛出多个异常时，尽量保证每个异常信息不一样或加上异常标识，方便调试与定位错误。如下例，若三个异常都提示用户excel读取失败，用户反馈回来后，开发人员无法通过错误信息定位到问题点：

15、所有和其他系统接口调用时发生的异常，建议都记录日志（文件日志或数据库日志），方便排查错误。推荐对接口增加统一的日志管理。比如统一加上@ApiLog注解

16、（S）不应该在try-catch语句块catch住Error时使用断言方法

17、（S）在断言中不应该进行类型不兼容的比较。

说明，避免判断总为真和总为假的情况，比如：

```java
// Noncompliant(不合规);基本数据类型永远不会为null
assertThat(size).isNotNull();
// Noncompliant;不相关的两个类的对象永远不会相等
assertThat(spatula).isNotEqualTo(tree);
// Noncompliant;非数组和数组永远不相等
assertThat(tool).isNotSameAs(tools);
// Noncompliant;不相关的数组永远不相等
assertThat(trees).isNotEqualTo(tools);
```

### 日志规约

1、所有日志文件**至少保存15天**，因为有些异常具备以“周”为频次发生的特点。

2、根据国家法律，网络运行状态、网络安全事件、个人敏感信息操作等相关记录，留存的日志**不少于六个月**，并且进行网络多机备份。

3、在日志输出时，字符串变量之间的拼接使用占位符的方式，否则在日志级别调高时，低级别的日志也会做字符串拼接处理。

```java
说明：因为String字符串的拼接会使用StringBuilder的append()方式，有一定的性能损耗。
使用占位符仅是替换动作，可以有效提升性能。
正例：logger.debug("Processing trade with id: {} and symbol: {}", id, symbol);
```

4、生产环境尽量不直接使用System.out 或System.err 输出日志或使用e.printStackTrace()打印异常堆栈。

```
说明：标准日志输出与标准错误输出文件每次Jboss重启时才滚动，如果大量输出送往这两个文件，
容易造成文件大小超过操作系统大小限制。
```

5、异常信息应该包括两类信息：案发现场信息和异常堆栈信息。如果不处理，那么通过关键字throws往上抛出。

```java
正例： logger.error("inputParams:{} and errorMessage:{}", 
                 各类参数或者对象.toString(), e.getMessage(), e);
```

6、谨慎地记录日志。生产环境禁止输出debug日志；有选择地输出info日志；如果使用warn来记录刚上线时的业务行为信息，一定要注意日志输出量的问题，避免把服务器磁盘撑爆，并记得及时删除这些观察日志。

```
说明：大量地输出无效日志，不利于系统性能提升，也不利于快速定位错误点。记录日志时
请思考：这些日志真的有人看吗？看到这条日志你能做什么？能不能给问题排查带来好处？
```

7、日志级别建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。

ERROR日志级别，指明错误事件，一般用于记录系统逻辑出错、异常或者重要的错误信息。

```java
try{
	reduceUserBalance(userInfo);
} catch (Exception ex){
	logger.error(“用户扣款失败:{}，错误信息:{}”, userInfo, ex);
}
```

WARN日志级别，指明可能潜在的危险状况，一般用于系统告警信息记录, 也可以使用warn日志级别来记录用户输入参数错误的情况，避免用户投诉时，无所适从。

```java
if(passwordErrorCount > 5){
	logger.warn(“用户：{}密码错误5次，锁定账号，当前IP：{}”, userCode, ip);
}
```

INFO日志级别，指明描述信息，从粗粒度上描述了应用的运行过程，一般用于线上业务逻辑记录必要日志使用。

```java
if(currentTime == ‘中午12点’){
	logger.info(‘定时任务开始执行’);
	// 定时任务业务逻辑
	…
	logger.info(‘任务执行成功’);
}
```

DEBUG日志级别，指明细致的应用信息，一般在开发环境下进行调试使用，线上环境不输出。

```java
int index = 0;
for(int i=0;i<10;i++){
	logger.debug(‘当前时间：{}，当前index：{}’, currentTime, index);
	//复杂业务逻辑
	…
	logger.debug(‘index：{}执行结束’, index);
}
```

