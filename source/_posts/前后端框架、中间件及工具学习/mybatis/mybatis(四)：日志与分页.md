---
title: mybatis(四)：日志与分页
categories: 前后端框架、中间件及工具学习
tags:
- mybatis
- 框架 
---

# 日志

## 日志工厂

- 如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手
- 曾经：sout、debug   |   现在：日志工厂
- 在mybatis-config.xml中用` <setting name="logImpl" value=""/>`配置

## 标准日志输出STDOUT_LOGGING

- 在mybatis-config.xml中用 `<setting name="logImpl" value="STDOUT_LOGGING"/>`配置
- 注意在别名`<typeAliases/>`前面
- 不需要额外添加任何东西,设置了就可以直接使用

## Log4j

## 因为有安全漏洞,所以不再详细展开

# 分页

## 为什么要分页

减少数据的处理量,使用Limit分页

## 使用Limit分页(sql语句中实现)

- 接口的对应方法的属性设成map类型：`List<User> getUserByLimit(HashMap<String,Integer> map)`
- Mapper.xml文件中直接取map对应的两个值
```xml
<mapper namespace="com.hanser.dao.UserMapper">
    <!--分页查询-->
    <!--用map来存储一组参数，pageIndex(页面大小)，startIndex(第一项是下标是0,页面从第几个开始)分别是map的key-->
    <select id="getUserByLimit" parameterType="hashmap" resultType="user">
        select * from mybatis.user limit #{startIndex},#{pageIndex}
    </select>
</mapper>
```
测试时,向map中加入key为startIndex和pageIndex的两个属性即可

## 使用RowBounds分页

不建议在开发中使用,了解即可,特点是不再使用SQL实现分页,通过一个类RowBounds在java代码层面实现分页

## 分页插件PageHelper

