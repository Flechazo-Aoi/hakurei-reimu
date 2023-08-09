---
title: mybatis(七)：多对多查询的实现
categories: java
tags:
- mybatis
- 框架 
---

# 多对一

## 准备工作

- 数据库中创建student和teacher表,并插入数据
- 创建实体类student

```java
public class Student {

    private int id;
    private String name;
    //学生要关联一个老师
    private Teacher teacher;
}
```
- 创建实体类teacher

```java
public class Teacher {
    private int id;
    private String name;
}
```
- 创建Mapper

## 多对一处理

### 方法一：按照查询嵌套处理   -->子查询

首先学生有一个老师属性,学生跟老师是多对一的（一个老师可以对应多个学生），回想mysql语句,要想得到所有学生及对应的老师,
就得先查出所有学生的信息,然后根据查出来的学生的tid，找出对应的老师

```mysql
select * from mybatis.student;
/*根据上面从数据库查到的student的tid取找对应的老师*/
select * from mybatis.teacher where id = #{id}
```
那么怎么把第一个的结果传给第二个查询以及怎么整合起来
```xml
    <select id="getStudentList" resultMap="StudentTeacher">
        select * from mybatis.student;
    </select>

    <resultMap id="StudentTeacher" type="student">
        <!--复杂的属性，我们需要单独处理对象，association代表一个对象,用于多对一，collection代表集合,用于一对多-->
        <association property="teacher" javaType="teacher" column="tid" select="getTeacher"/>
    </resultMap>
    
    <select id="getTeacher" resultType="teacher">
        select * from mybatis.teacher where id = #{id}
    </select>

```
### 方法二：按照结果嵌套查询(就是联表查询)

```mysql
select s.id,s.name,t.name from mybatis.student s,mybatis.teacher t where s.tid = t.id;
```
```xml
<!--写一个大的查询语句,起好别名,返回值类型用结果集映射来写-->
<select id="getStudentList2" resultMap="ST">
        select s.id sid,s.name sname,t.id tid,t.name tname
        from mybatis.student s,mybatis.teacher t
        where s.tid = t.id
    </select>
<!--跟别名一致,如果属性是一个类对象,就要用到association-->
    <resultMap id="ST" type="student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="teacher">
            <result property="name" column="tname"/>
            <result property="id" column="tid"/>
        </association>
    </resultMap>
```
# 一对多

一个老师拥有多个学生。对于老师而言，就是一对多的关系

## 环境

环境和上面差不多,student去掉老师类属性

在老师类加入一个属性：学生类的集合,代表一个老师管多个学生

## 一对多处理

### 方法一：按照结果嵌套查询

```xml
    <!--按照结果嵌套查询-->
    <select id="getTeacher" resultMap="ST">
        select t.id tid,t.name tname,s.id sid,s.name sname
        from mybatis.teacher t,mybatis.student s
        where t.id = s.tid and t.id = #{tid}
    </select>

    <resultMap id="ST" type="teacher">
        <result column="tid" property="id"/>
        <result column="tname" property="name"/>
        <!--复杂的属性，我们需要单独处理对象，association代表一个对象，collection代表集合
            javaType 是一个类指定属性的类型
            集合中的泛型信息，我们使用ofType获取-->
        <collection property="studentList" ofType="student">
            <result property="id" column="sid"/>
            <result property="name" column="sname"/>
        </collection>
    </resultMap>

```
### 方法二：按照查询嵌套查询,子查询

```xml
    <select id="getTeacher2" resultMap="ST2">
        select *
        from mybatis.teacher
        where id = #{tid}
    </select>

    <select id="getStudent" resultType="student">
        select *
        from mybatis.student
        where tid = #{tid}
    </select>

    <resultMap id="ST2" type="teacher">
        <!--不加下面这一行老师查出来id为0，因为resultMap默认替换同名字段为属性，这里显式的把id付给了collection
            resultMap就以为不用在输出id了，显示添加下面这一行就行-->
        <result property="id" column="id"/>
        <collection property="studentList" ofType="student" javaType="ArrayList" column="id" select="getStudent"/>
    </resultMap>

```
