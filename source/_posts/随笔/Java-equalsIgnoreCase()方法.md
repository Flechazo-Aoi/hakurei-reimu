---
title: Java equalsIgnoreCase()方法
categories: 随笔
tags: 
- java基础
---

`equalsIgnoreCase() `方法用于将字符串与指定的对象比较，不考虑大小写。

```java
/**
 * @param  anotherString -- 与字符串进行比较的对象。
 * @return  如果给定对象与字符串相等，则返回 true，否则返回 false。
 */
public boolean equalsIgnoreCase(String anotherString)
```

equals() 会判断大小写区别，equalsIgnoreCase() 不会判断大小写区别：

```java
public class Test {
    public static void main(String args[]) {
        String Str1 = new String("runoob");
        String Str2 = Str1;
        String Str3 = new String("runoob");
        String Str4 = new String("RUNOOB");
        boolean retVal;

        retVal = Str1.equals( Str2 );
        System.out.println("返回值 = " + retVal );   // true

        retVal = Str3.equals( Str4);
        System.out.println("返回值 = " + retVal );   // false

        retVal = Str1.equalsIgnoreCase( Str4 );
        System.out.println("返回值 = " + retVal );   // true
    }
}
```

