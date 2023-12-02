---
title: java基础随笔11.27-12.02
categories: java基础
date: 2023-12-02  15:22:33
tags: 
- java
- 随笔
---

## Java中更换Map中的键key的名称

在项目中，经常会遇到需要调整查询结果map中key的值，以便后面逻辑的执行，我们可以采用以下方式来实现

```java
map.put("newKey", map.remove("oldKey"));
```

上述语句可以实现将map中的`oldKey`变更为`newKey`，同时，根据上述语句也不难看出：`map.remove("key")`的返回值为`key`对应的`value`

此外，如果查询结果为一个map的list，可以循环替换

```java
list.forEach(map -> {
    map.put("newKey1", map.remove("oldKey1"));
    map.put("newKey2", map.remove("oldKey2"));
});
```

## String与Date的转换

### DateFormat

String转Date

```java
DateFormat format1 = new SimpleDateFormat("yyyy-MM-dd");
DateFormat format2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String s ="2023-12-02";
String S2= "2023-12-02 16:21:02 ";
Date date = format1.parse(s);
Date date2 = format2.parse(S2);
System.out.println("date="+date);    // date = Sat Dec 09 00:00:00 CST 2023
System.out.println("date2="+date2);  // date2 = Sun Dec 10 16:21:02 CST 2023
```

Date转String

```java
Date datex = new Date();
DateFormat format3 = new SimpleDateFormat("yyyy-MM-dd");
DateFormat format4 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println("format3="+format3.format(datex));   // format3=2023-12-02
System.out.println("format4="+format4.format(datex));   // format4=2023-11-02 15:39:59
```

