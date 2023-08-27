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