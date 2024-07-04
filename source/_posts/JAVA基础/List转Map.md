---
title: List<Map>转Map<>
categories: java基础
date: 2024-04-02  15:22:33
tags: 
- java
- 随笔
---

## 将List<Map<String, String>> 转化为Map<String, String>

### 第一种情况：

旧map的两个value作为新map的(key, value)

```java
    List<Map<String, String>> listMap = new ArrayList<>();
    Map<String, String> map1 = new HashMap<>();
    Map<String, String> map2 = new HashMap<>();
    Map<String, String> map3 = new HashMap<>();
    listMap.add(map1);
    listMap.add(map2);
    listMap.add(map3);
    map1.put("name","yuwen");
    map1.put("code","01");
    map2.put("name","shuxu");
    map2.put("code","02");
    map3.put("name","yingyu");
    map3.put("code","03");

    //期望转为 {03=yingyu, 01=yuwen, 02=shuxu}
```

方法：

```java
Map<String, String> collect = listMap.stream().collect(
        Collectors.toMap(
                t -> t.get("code"),		// code作为新map的key
                t -> t.get("name"),		// name作为新map的value
                (o, n) -> n			    // 如果两个key相等，则采用第一个（根据放入list的顺序区分那个是第一个，那个是第二个）
        )
);
```

注意：如果不加`(o, n) -> n`，并且多个map中存在相同的key，则执行会报错

### 第二种情况：

旧map的key作为新map的key，旧map的value作为新map的value

```java
Map<String,Object> h1 = new HashMap<>();
h1.put("12","fdsa");
h1.put("123","fdsa");
h1.put("124","fdsa");
h1.put("125","fdsa");

Map<String,Object> h2 = new HashMap<>();
h2.put("h12","fdsa");
h2.put("h123","fdsa");
h2.put("h124","fdsa");
h2.put("h125","fdsa");

Map<String,Object> h3 = new HashMap<>();
h3.put("h312","fdsa");
h3.put("h3123","fdsa");
h3.put("h3124","fdsa");
h3.put("h3125","fdsa");

List<Map<String,Object>> lists = new ArrayList<>();
lists.add(h1);
lists.add(h2);
lists.add(h3);

// 期望转成一个map，map中包含h1、h2、h3所有的key和对应的value
```

方法：

```java
Map<String, Object> merged = lists.stream()
        .map(Map::entrySet)
        .flatMap(Set::stream)
        .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (x, y) -> x));
```

注意的内容同上