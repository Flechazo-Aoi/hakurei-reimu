---
title: maven处理重复依赖不同版本的方式
categories: java基础
date: 2023-12-12 15:22:33
tags: 
  - java
  - 随笔
---

## 前言

大家在处理maven依赖时，肯定都有遇到过包冲突的问题，其中最常见的就是在多级依赖时，会同时引入一个jar包的不同版本，导致在运行时出现NoSuchMethodError的错误，那么大家肯定会好奇对于这些情况maven是怎么去选择版本的呢？其中网上挺多文章已经都解密了它的处理方式，我这里先把这些方式抛出来，然后一个个的去验证它们。
当一个项目中出现重复的依赖包时，maven 2.0.9之后的版本会用如下的规则来决定使用哪一个版本的包：

```
1.最短路径原则：多级依赖不同路径
2.声明优先原则：多级依赖相同路径
3.同级依赖后加载覆盖先加载：直接同级依赖
```

## 分析与验证

准备：创建一个验证工程已经多个验证模块，并根据校验场景来引入依赖关系，其中作为验证对象的testD构建两个不同版本（1.0和2.0）的jar

![1708653421332](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402230957076.png)

### 最短路径原则

#### 分析

对于服务A直接依赖了服务B和E，其中B和E都直接或间接的引用了D的两个版本1.0和2.0，此时根据最短路径原则，A最终引用的应该是D的2.0版本

![1708653477608](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402230958233.png)

#### 验证

![1708653543551](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402230959813.png)

![1708653555871](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231000750.png)

![1708653555871](C:\Users\16646\Downloads\1708653573659.png)

![1708653620812](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231000854.png)

通过对A执行mvn dependency:tree 命令来分析A的依赖树（进入A模块`pom.xml`文件所在路径下执行命令）

通过依赖树结果来看，最终依赖的是D的2.0版本，所以验证了maven的最短路径原则；

### 声明优先原则

#### 分析

A服务依赖C和E，其中C依赖D的1.0版本，E依赖D的2.0版本，其中C已依赖声明在E之前，此时根据声明优先原则，相同的依赖路径，会安装依赖引入的先后顺序进行选择，选择最先引入的版本，所以应该是选择1.0版本；

![1708653769676](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231002609.png)

将E和C的依赖顺序交换，此次应该选择的是D的2.0版本

#### 验证

A服务依赖C和E，其中C依赖D的2.0版本，E依赖D的1.0版本，其中C已依赖声明在E之前：

![1708653826197](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231003937.png)

通过对A执行mvn dependency:tree 命令来分析A的依赖树，最终引入的是D的1.0版本

![1708653884425](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231004692.png)

将E和C的依赖顺序交换：

![1708653930372](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231005499.png)

通过对A执行mvn dependency:tree 命令来分析A的依赖树，最终引入的是D的2.0版本

![1708653964197](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231006835.png)

### 同级依赖，后加载覆盖先加载

#### 分析

A直接依赖D的1.0和2.0版本，1.0的依赖声明顺序早于2.0版本：此时2.0版本会覆盖1.0版本从而最终选择2.0版本；

![1708654044753](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231007223.png)

如果交换1.0和2.0的依赖声明顺序，2.0早于1.0，此时1.0版本会覆盖2.0版本，从而最终选择2.0版本

#### 验证

A直接依赖D的1.0和2.0版本，1.0的依赖声明顺序早于2.0版本：

![1708654104563](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231008740.png)

通对A执行mvn dependency:tree 命令来分析A的依赖树，最终引入的是D的2.0版本

![1708654127716](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231009102.png)

交换1.0和2.0的依赖声明顺序，2.0早于1.0：

![1708654196488](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231010558.png)

通对A执行mvn dependency:tree 命令来分析A的依赖树，最终引入的是D的1.0版本

![1708654219055](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202402231010345.png)

## 总结

- **最短路径原则：对于多级依赖出现相同jar的不同版本，maven会选择路径最短的依赖；**
- **声明优先原则：对于多级依赖出现相同jar的不同版本，并且所经历的路径相同时，maven会选择最先声明的依赖版本；**
- **同级依赖，后声明会覆盖先声明原则：对于同一级的依赖出现相同jar的不同版本，maven会根据依赖声明的先后顺序，选择后声明的依赖版本；**

