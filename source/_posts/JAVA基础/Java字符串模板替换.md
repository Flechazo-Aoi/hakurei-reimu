---
title: Java字符串模板替换
categories: java基础
date: 2024-01-16 15:22:33
tags: 
  - java
  - 随笔
---

设置一个模板字符串，字符串中设置模板字段，可以使用变量来替换模板字段

## 使用内置String.format

```java
String message = String.format("您好%s，晚上好！您目前余额：%.2f元，积分：%d", "张三", 10.155, 10);
System.out.println(message);
// 您好张三，晚上好！您目前余额：10.16元，积分：10
```

其中`%s`、`%.2f`是不同的转换符，对应不同的数据类型，与C语言的`printf()`类似

## **使用内置MessageFormat** 

`MessageFormat.format`默认替换`{}`内容，与日志输出字符串替换类似。

```java
String message = MessageFormat.format("您好{0}，晚上好！您目前余额：{1,number,#.##}元，积分：{2}", "张三", 10.155, 10);
System.out.println(message);
// 您好张三，晚上好！您目前余额：10.16元，积分：10
```

## 自定义封装

我们需要自定义要替换的字段，比如传入为一个map，key为待替换字段，value为替换字段

```java
// 在使用正则表达式时，Pattern 要定义为 static final 静态变量，利用好其预编译功能，以避免执行多次预编译。可以有效加快正则匹配速度。
static Pattern compile = Pattern.compile("\\{\\{\\w+\\}\\}");

/**
 * 自定义模板解析方法
 *
 * @param template : 消息内容模板
 * @param params   : 待替换map
 * @return String : 转换后的消息内容
 * @author zhc
 * @date 2024/1/15
 **/
public static String processTemplate(String template, Map<String, String> params) {
    Matcher m = compile.matcher(template);
    StringBuffer sb = new StringBuffer();
    while (m.find()) {
        String param = m.group();
        String value = params.get(param.substring(2, param.length() - 2));
        m.appendReplacement(sb, value == null ? "" : value);
    }
    m.appendTail(sb);
    return sb.toString();
}
```

上述方法实现结果为，将`template`字符串中的`{{key}}`替换成`value`