---
title: IDEA使用Git管理项目
date: 2022-11-15 22:21:43
categories: IDEA使用
tags:
- IDEA
---

### 下载git
地址：https://git-scm.com/download/win
### 安装
无特殊需求一路默认即可
### 在命令提示符中验证安装是否成功（显示版本号）
![1.jpg](/img/img.png)
### IDEA中配置Git  
file->Settings，左上角搜索Git
![2.jpg](/img/img_1.png)
### 在github上创建一个仓库，并获得远程仓库地址
![3.jpg](/img/img_2.png)
### 提交到远程仓库Github
#### 使用IDEA绑定github账户
需要用到个人访问令牌,详情见7
#### 指定本地仓库
VCS - > Create Git Repository
![4.jpg](/img/img_3.png)
#### 提交到远程仓库。
选中项目文件夹右键->Git->Commit Directory
![5.jpg](/img/img_4.png)
#### 第一次提交
如果是第一次提交需要指定远程仓库，即在Github创建的repository
![6.jpg](/img/img_5.png)
#### 选择提交哪些文件和提交信息。
忽略弹出的警告信息继续提交（commit）
![7.jpg](/img/img_6.png)
### 创建个人访问令牌步骤
- 1.头像 -> Settings
- 2.在左侧边栏中，点击 Developer settings
- 3.在左侧边栏中，点击 Personal access tokens（个人访问令牌） 
- 4.点击 Generate new token（生成新令牌）
- 5.设置令牌有效时间 
- 6.选择要授予此令牌的作用域或权限。 要使用令牌从命令行访问仓库，请选择 repo（仓库）
- 7.点击 Generate token（生成令牌）
- 8.单击 将令牌复制到剪贴板。 出于安全原因，在离开页面后，将无法再次看到令牌

**注意：要像对待密码一样对待你的令牌，确保其机密性。 使用 API 时，应将令牌用作环境变量，而不是将其硬编码到程序中**

