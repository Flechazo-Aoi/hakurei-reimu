---
title: IDEA使用随笔
categories: IDEA使用
tags:
- IDEA
- 随笔
---

## idea右边找不到maven窗口不见了

右侧边栏没有出现maven, 可能就是pom.xml文件没有识别, idea觉得这个项目就不是个maven项目，导致idea无法加载依赖包。

直接右键pom.xml，然后点击Add as Maven Project即可

![image-20230827094827664](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308270948221.png)

## idea配置全局maven

idea每次创建新项目都会默认使用C盘的.m2 Maven仓库，如下图

![image-20230827095115917](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308270951956.png)

这就使得我们每次创建或打开一个新项目，都要重新配置maven，十分麻烦。

实际上，IDEA为新项目提供了一个全局的配置，如下图

![image-20230827095338822](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308270953867.png)

我们可以在这里对新项目进行一个全局的配置，包括maven配置

## IDEA无法识别Java文件

如下图，无法正常识别.java文件

![image-20230827133324276](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308271333382.png)

我们只需要把其上级目录的java文件夹设置为Sources Root即可

![image-20230827133415800](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202308271334869.png)

## IDEA设置Java类注释和方法注释面板

### 类注释模板设置

在`File` –> `settings` –> `Editor` –> `File and Code Templates` –> `Files`

![image-20230910181711006](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101817131.png)

然后就可以自定义注释，可以参考如下形式：

```java
/**  
 * @author       : Sonetto 
 * @description  : ${NAME} 
 * @createTime   : ${DATE} ${TIME}   
 * @updateTime   : ${DATE} ${TIME}  
 * @updateRemark :   
 */
```

### 方法注释模板

方法注释模版和类注释类似，位置在`File` –> `Settings` –> `Editor` –> `Live Templates`

1. 新建组，起个名，比如命名为userDefine，

2. 新建模板Live Templates

   ![image-20230910182745760](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101827829.png)

3. 设置模板匹配的方法，添加模板描述（`/*+模板名+Enter`）

   因为javadoc注释的格式为`/**  ...`，所以我们在这里设置模板名为`*`，后续生成按键设为enter，后续在方法上输入

   `/** + Enter`就会自动生成设置的模板内容

4. 设置模板生成按键，这里设置的是enter

5. 编写模板内容

   注意第一行，只有一个`*`而不是`/*`

   在设置参数名时必须用`$参数名$`的方式，否则最后一步中读取不到你设置的参数名

   ![image-20230910185126004](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101851077.png)

6. 设置模板的应用场景

   一般选择`everywhere` -> `java`即可，因为是给java方法加注释

   ![image-20230910184353880](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101843927.png)

7. 设置参数的获取方式

![image-20230910184826646](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202309101848693.png)

至此，设置完成