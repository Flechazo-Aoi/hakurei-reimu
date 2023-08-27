---
title: java开发规范-安全规范
categories: java基础
tags:
- java
- 开发规范
---

## 安全规约

1、隶属于用户个人的页面或者功能必须进行权限控制校验。

```java
说明：防止没有做水平权限校验就可随意访问、修改、删除别人的数据，比如查看他人的私信内容。举例说明：
若用户请假单仅允许用户自己删除，那么需要在delete语句中，加上createuser的验证条件：
delete from doc where id='' and createuser='sessionuser.user.id'
```

2、用户敏感数据禁止直接展示，必须对展示数据进行脱敏

```java
for (Map<String, Object> user : tempList) {
	user.remove("userpassword")
}
```

3、用户输入的SQL参数严格使用参数绑定或者METADATA字段值限定，防止SQL注入，禁止字符串拼接SQL访问数据库。

这里涉及MySQL中#{}和${}的区别

- \#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。如：order by #user_id#，如果传入的值是111,那么解析成sql时的值为order by “111”, 如果传入的值是id，则解析成的sql为order by “id”.
- $将传入的数据直接显示生成在sql中。如：order by user_id，如果传入的值是111,那么解析成sql时的值为order by user_id, 如果传入的值是id，则解析成的sql为order by id.
- \#{}: 解析为一个 JDBC 预编译语句（prepared statement）的参数标记符，一个 #{ } 被解析为一个参数占位符 。
- ${}: 仅仅为一个纯碎的 string 替换，在动态 SQL 解析阶段将会进行变量替换。

结论：\#方式能够很大程度防止sql注入。而$方式无法防止Sql注入。

mybatis的xml文件中的变量，优先使用#{param}的形式，防止sql注入，尽量不使用${}，或者在使用${}进行数据库关键字过滤。

4、用户请求传入的任何参数必须做有效性验证。说明：忽略参数校验可能导致：

- page size过大导致内存溢出
- 恶意order by导致数据库慢查询
- 缓存击穿
- SSRF
- 任意重定向
- SQL注入，Shell注入，反序列化注入
- 正则输入源串拒绝服务ReDoS

Java代码用正则来验证客户端的输入，有些正则写法验证普通用户输入没有问题，但是如果攻击人员使用的是特殊构造的字符串来验证，有可能导致死循环的结果。

5、禁止向HTML页面输出未经安全过滤或未正确转义的用户数据。

6、（S）表单、AJAX提交必须执行CSRF安全验证。

```
说明：CSRF(Cross-site request forgery)跨站请求伪造是一类常见编程漏洞。对于存在CSRF漏洞的应用/网站，
攻击者可以事先构造好URL，只要受害者用户一访问，后台便在用户不知情的情况下对数据库中用户参数进行相应修改。
```

7、URL外部重定向传入的目标地址必须执行白名单过滤。

8、在使用平台资源，譬如短信、邮件、电话、下单、支付，必须实现正确的防重放的机制，如数量限制、疲劳度控制、验证码校验，避免被滥刷而导致资损。

```
说明：如注册时发送验证码到手机，如果没有限制次数和频率，那么可以利用此功能骚扰到其它用户，并造成短信平台资源浪费。
```

9、发贴、评论、发送即时消息等用户生成内容的场景必须实现防刷、文本内容违禁词过滤等风控策略。

10、（S）连接数据库时应使用安全密码，不允许出现无密码的数据库连接。

```java
反例：
Connection conn = DriverManager.getConnection("jdbc:derby:memory:myDB;create=true","login", "");
正例：
String password = System.getProperty("database.password");
Connection conn = DriverManager.getConnection("jdbc:derby:memory:myDB;create=true","login", password);
```

