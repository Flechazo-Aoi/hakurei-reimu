---
title: springMVC(五)：json
categories: java
tags:
- spring
- 框架 
- springMVC
---

## 什么是JSON？

- JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛。
- 采用完全独立于编程语言的**文本格式**来存储和表示数据。
- 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率

在JavaScript语言中，一切都是对象。因此，任何JavaScript 支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等。看看他的要求和语法格式：

- 一般对象和String表示为键值对，数据由逗号分隔
- 花括号保存引用对象
- 方括号保存数组

```xml
<!--JSON 键值对是用来保存 JavaScript 对象的一种方式，和 JavaScript 对象的写法也大同小异，键/值对组合中的键名写在前面并用双引号 "" 包裹，使用冒号 : 分隔，然后紧接着值：-->
        {"name": "QinJiang"}
        {"age": "3"}
        {"sex": "男"}
```

很多人搞不清楚 JSON 和 JavaScript 对象的关系，甚至连谁是谁都不清楚。其实，可以这么理解：

- JSON 是 JavaScript 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。
- JSON 和 JavaScript 对象之间是可以互相转换的

```xml
<!--要实现从JSON字符串转换为JavaScript 对象，使用 JSON.parse() 方法：-->
        var obj = JSON.parse('{"a": "Hello", "b": "World"}');
        //结果是 {a: 'Hello', b: 'World'}
        <!--要实现从JavaScript 对象转换为JSON字符串，使用 JSON.stringify() 方法：-->
        var json = JSON.stringify({a: 'Hello', b: 'World'});
        //结果是 '{"a": "Hello", "b": "World"}'
```

```xml

<script type="text/javascript">
    <!--编写一个js对象,let是一个类型,相当于之前的var,表示所有类型-->
    let user = {
    name:"hanser",
    age:3,
    gender:"男"
    };

    <!--将js对象转换为json字符串-->
    let str = JSON.stringify(user);
    <!--在前端的控制台中输出,是一个字符串类型的,不能展开-->
    console.log(str);

    <!--将json字符串转换为js对象-->
    let user2 = JSON.parse(str);
    <!--在前端的控制台中输出,是一个对象,可以展开-->
    console.log(user2);
</script>
```

## 在Controller中返回JSON数据之jackson

### 准备工作

- 1.配置Jackson依赖 Jackson应该是目前比较好的json解析工具了.当然工具不止这一个，比如还有阿里巴巴的 fastjson 等等。 我们这里使用Jackson，使用它需要导入它的jar包；

```xml
<!--Jackson-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.10.0</version>
</dependency>
```

- 2.配置SpringMVC需要的配置,web.xml,springmvc-servlet.xml和之前一样
- 3.写一个用于测试的User类

```java

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private int age;
    private String gender;
}
```

### 实现一:返回一般对象的json格式

- 4.写对应的Controller类

用到两个新东西:@ResponseBody和ObjectMapper对象

- @ResponseBody:原本@Controller注解的类里所有返回值为String的方法的返回值都会被自动被视图解析器解析 ,加上之后,会直接返回一个字符串,而不会被当成一个逻辑视图
- ObjectMapper:jackson的对象映射器,用来解析数据.调用.writeValueAsString()方法来把我们的对象解析成jason格式

```java
import com.fasterxml.jackson.databind.ObjectMapper;

@Controller
public class UserController {

    @RequestMapping("/json1")
    @ResponseBody   //有这个注解后,他就不会走视图解析器,会直接返回一个字符串
    public String json1(Model model) {
        //创建一个jackson的对象映射器，用来解析数据
        ObjectMapper mapper = new ObjectMapper();
        //创建一个对象
        User user = new User("憨色", 20, "男");
        //将我们的对象解析成为json格式
        String str = mapper.writeValueAsString(user);
        return str;
    }
}
```

经过上面操作后,我们发现输出有乱码,而我们在配置文件中配置了过滤器来处理乱码, 那个过滤器只会处理请求乱码,而json不是请求,所以不会处理.解决方案:

- @RequestMapping有一个produces属性

```java
//produces:指定响应体返回类型和编码
@RequestMapping(value = "/json1", produces = "application/json;charset=utf-8")
```

这样就能解决问题,这是原生态的方法

- 使用springMVC自带的json乱码配置(在springMVC的配置文件中)

```xml
<!--处理json乱码-->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="utf-8"></constructor-arg>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

### 实现二:返回list的json格式

我们发现,如果让java返回json,那么在@Controller注解的类下每写一个方法,就要@ResponseBody,十分麻烦, 所以自然而然地想到,有没有方法可以一劳永逸:**
类上的注解从@Controller变成@RestController**
这样,这个类下的所有方法都会直接返回字符串,而不再进视图解析器

- 测试集合输出:

```java
//测试集合输出
@RequestMapping("/json2")
public String json2()throws JsonProcessingException{
        //创建一个jackson的对象映射器，用来解析数据
        ObjectMapper mapper=new ObjectMapper();
        List<User> list=new LinkedList<>();
        User user1=new User("憨色",20,"男");
        User user2=new User("憨色",20,"男");
        User user3=new User("憨色",20,"男");
        User user4=new User("憨色",20,"男");
        list.add(user1);
        list.add(user2);
        list.add(user3);
        list.add(user4);
        String str=mapper.writeValueAsString(list);
        return str;
        }
```

```java
[{"name":"憨色","age":20,"gender":"男"},{"name":"憨色","age":20,"gender":"男"},
        {"name":"憨色","age":20,"gender":"男"},{"name":"憨色","age":20,"gender":"男"}]
```

结果为[]括起来的,也就是数组形式

### 实现三:输出时间对象

```java
//测试日期输出
@RequestMapping("/json3")
public String json3()throws JsonProcessingException{
        ObjectMapper mapper=new ObjectMapper();
        Date date=new Date();
        //默认日期格式(Wed Aug 03 21:28:39 CST 2022)会变成一个数字，是1970年1月1日到当前日期的毫秒数！
        //Jackson 默认是会把时间转成timestamps形式
        String str=mapper.writeValueAsString(date);
        return str;
        }
```

我们发现日期会被转换成1970年1月1日到当前日期的毫秒数！(timestamps形式),我们可以自定义格式

- 1.使用java先转日期格式,在交由ObjectMapper解析

```java
//测试日期输出
@RequestMapping("/json3")
public String json3()throws JsonProcessingException{
        ObjectMapper mapper=new ObjectMapper();
        Date date=new Date();
        //自定义日期格式.格式的大小写由规定,MM月,h 时 在上午或下午 (1~12);H 时 在一天中 (0~23)
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //sdf.format(date),将日期格式化,再交给mapper解析
        String str=mapper.writeValueAsString(sdf.format(date));
        return str;
        }
```

- 2.使用ObjectMapper的配置信息,取消默认的timestamps形式,改成自定义形式

```java
@RequestMapping("/json4")
public String json4()throws JsonProcessingException{
        ObjectMapper mapper=new ObjectMapper();
        //不使用时间戳的方式,关闭timestamps形式
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        //自定义日期格式对象
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //指定日期格式
        mapper.setDateFormat(sdf);
        Date date=new Date();
        String str=mapper.writeValueAsString(date);
        return str;
        }
```

### 优化

实现上面三种方式后,我们不难发现,三个方法中,有一些公共的部分,比如创建ObjectMapper类对象, 比如调用.writeValueAsString()方法,这时我们就在想,**能不能自己创建一个工具类,接受参数,
并返回结果,我们的代码只需要调用这个类的方法即可**,这种思想很重要

- 编写json工具类JsonUtils

```java
//json工具类,里面的方法接受参数,返回json对象
public class JsonUtils {
    public static String getJson(Object object) {
        return getJson(object, "yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object object, String dateFormat) {
        ObjectMapper mapper = new ObjectMapper();
        //不使用时间戳的方式
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
        //自定义日期格式对象
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        //指定日期格式
        mapper.setDateFormat(sdf);
        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        //出了异常就return null
        return null;
    }
}
```

这里有一个值的注意的点:因为传入的可能日期,所以我们其中一个方法要有日期的格式这个参数, 而如果传入的不是日期,就不需要这个参数,所以采用了**方法重载**,而写那个参数少的方法时, 我们并没有直接复制那些公共的代码,而是**
直接调用的参数多的方法,并给不需要的参数设置一个默认值**
这是从开源项目的源码中学到的,这样可以**不用重复造轮子**

有了这个工具类,我们Controller类的方法就很简单且纯粹

```java

@RestController
public class UserController {

    @RequestMapping("/json1")
    public String json1() throws JsonProcessingException {
        User user = new User("憨色", 20, "男");
        return JsonUtils.getJson(user);
    }

    //测试集合输出
    @RequestMapping("/json2")
    public String json2() throws JsonProcessingException {
        List<User> list = new LinkedList<>();
        User user1 = new User("憨色", 20, "男");
        User user2 = new User("憨色", 20, "男");
        User user3 = new User("憨色", 20, "男");
        User user4 = new User("憨色", 20, "男");
        list.add(user1);
        list.add(user2);
        list.add(user3);
        list.add(user4);
        return JsonUtils.getJson(list);
    }

    //测试日期输出
    @RequestMapping("/json3")
    public String json3() throws JsonProcessingException {
        Date date = new Date();
        String dateFormat = "yyyy-MM-dd HH:mm:ss";
        return JsonUtils.getJson(date, dateFormat);
    }

}

```

## 在Controller中返回JSON数据之FastJson

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，
实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的。

### 准备工作:

```xml
<!--fastjson 的 pom依赖！-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.60</version>
</dependency>
```

### fastjson 三个主要的类：

JSONObject 代表 json 对象

- JSONObject实现了Map接口, 猜想 JSONObject底层操作是由Map实现的。
- JSONObject对应json对象，通过各种形式的get()方法可以获取json对象中的数据，也可利用诸如size()，isEmpty()等方法获取"键：值"对的个数和判断是否为空。其本质是通过实现Map接口并调用接口中的方法完成的。

JSONArray 代表 json 对象数组

- 内部是有List接口中的方法来完成操作的。

JSON代表 JSONObject和JSONArray的转化

- JSON类源码分析与使用
- 仔细观察这些方法，主要是实现json对象，json对象数组，javabean对象，json字符串之间的相互转化。

### 代码测试
```java
public class FastJsonDemo {
    public static void main(String[] args) {
        //创建一个对象
        User user1 = new User("秦疆1号", 3, "男");
        User user2 = new User("秦疆2号", 3, "男");
        User user3 = new User("秦疆3号", 3, "男");
        User user4 = new User("秦疆4号", 3, "男");
        List<User> list = new ArrayList<User>();
        list.add(user1);
        list.add(user2);
        list.add(user3);
        list.add(user4);

        System.out.println("*******Java对象 转 JSON字符串*******");
        String str1 = JSON.toJSONString(list);
        System.out.println("JSON.toJSONString(list)==>"+str1);
        String str2 = JSON.toJSONString(user1);
        System.out.println("JSON.toJSONString(user1)==>"+str2);

        System.out.println("\n****** JSON字符串 转 Java对象*******");
        User jp_user1=JSON.parseObject(str2,User.class);
        System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

        System.out.println("\n****** Java对象 转 JSON对象 ******");
        JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
        System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

        System.out.println("\n****** JSON对象 转 Java对象 ******");
        User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
        System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);
    }
}
```
这种工具类，我们只需要掌握使用就好了，在使用的时候在根据具体的业务去找对应的实现。和以前的commons-io那种工具包一样，拿来用就好了！
