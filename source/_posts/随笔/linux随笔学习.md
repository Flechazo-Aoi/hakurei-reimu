---
title: linux随笔学习
categories: 随笔
tags: 	
  - linux
---

- `>`：标准重定向符允许我们创建一个 0KB 的空文件。
- `touch`：如果文件不存在的话，`touch` 命令将会创建一个 0KB 的空文件。
- `echo`：通过一个参数显示文本的某行。
- `printf`：用于显示在终端给定的文本。
- `cat`：它串联并打印文件到标准输出。
- `vi`/`vim`：Vim 是一个向上兼容 Vi 的文本编辑器。它常用于编辑各种类型的纯文本。
- `nano`：是一个简小且用户友好的编辑器。它复制了 `pico` 的外观和优点，但它是自由软件。
- `head`：用于打印一个文件开头的一部分。
- `tail`：用于打印一个文件的最后一部分。
- `truncate`：用于缩小或者扩展文件的尺寸到指定大小

## Linux一些目录含义

- **/**  : 用户根目录
- **/bin（binary）**  :  二进制执行文件目录，系统的一些指令，主要用于具体应用
- **/sbin（system  binary）**  :    超级用户指令系统管理命令，这里存放的是系统管理员使用的管理程序，主要用于系统管理
- **/usr/bin**  :  后期安装的一些软件的安装脚本
- **/usr/sbin**  :  超级用户的一些管理程序
- **/usr/X11R6/bin**: X application user commands
- **/usr/X11R6/sbin**: X application super usercommands

一些重要目录：

- 主目录：/root、/home/username
- 用户可执行文件：/bin、/usr/bin、/usr/local/bin
- 系统可执行文件：/sbin、/usr/sbin、/usr/local/sbin
- 其他挂载点：/media、/mnt
- 配置：/etc
- 临时文件：/tmp
- 内核和Bootloader：/boot
- 服务器数据：/var、/srv
- 系统信息：/proc、/sys
- 共享库：/lib、/usr/lib、/usr/local/lib

每个用户都拥有一个主目录。所有用户的个人文件（配置、数据甚至应用程序）都放在其中。根的主目录为/root。大多数非根主目录包含在/home 树中，通常以用户命名。 重要的二进制位于 /bin（用户二进制）以及 /sbin（系统二进制）中。 不重要的二进制（如图形环境或Office 工具）安装在/usr/bin 和 /usr/sbin中。 进行这种分隔是为了尽可能地缩小根分区。 使用源代码编译的软件通常位于 /usr/local/bin 和/usr/local/sbin中。
