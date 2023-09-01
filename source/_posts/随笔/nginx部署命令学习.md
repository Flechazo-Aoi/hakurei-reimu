---
title: nginx部署命令学习
categories: 随笔
tags: 	
  - nginx
  - linux
---

## 安装依赖

```bash
yum -y install wget make cmake gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

安装wget、cmake、gcc、pcre、zlip、openssl依赖

### yum 命令

yum（ Yellow dog Updater, Modified）是一个在 Fedora 和 RedHat 以及 SUSE 中的 Shell 前端软件包管理器。

基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

yum 提供了查找、安装、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。

#### 语法

```bash
yum [options] [command] [package ...]
```

- options: 可选，选项包括-h(帮助)，-y(安装过程中所有选项都选yes)，-q(不显示安装的过程)等等
- command: 要执行的操作，比如update、install等等
- package: 安装的包名

#### 常用命令：

1. 列出所有可更新的软件清单命令：**yum check-update**
2. 更新所有软件命令：**yum update**
3.  仅安装指定的软件命令：**yum install <package_name>**
4. 仅更新指定的软件命令：**yum update <package_name>**
5. 列出所有可安裝的软件清单命令：**yum list**
6. 删除软件包命令：**yum remove <package_name>**
7. 查找软件包命令：**yum search <keyword>**
8. 清除缓存命令:
   - **yum clean packages**: 清除缓存目录下的软件包
   - **yum clean headers**: 清除缓存目录下的 headers
   - **yum clean oldheaders**: 清除缓存目录下旧的 headers
   - **yum clean, yum clean all (= yum clean packages; yum clean oldheaders)** :清除缓存目录下的软件包及旧的 headers

## 下载编译并安装

```bash
# 从网络上下载nginx压缩包
wget http://nginx.org/download/nginx-1.16.0.tar.gz 
# 打包/解压nginx压缩包文件
tar -xf nginx-1.16.0.tar.gz 
cd nginx-1.16.0 
# 编译前准备 
./configure \ 
--prefix=/opt/apps/nginx \ 
--conf-path=/opt/apps/nginx/conf/nginx.conf \ 
--sbin-path=/opt/apps/nginx/sbin/nginx \ 
--with-http_stub_status_module \ 
--with-http_ssl_module \ 
--with-http_gunzip_module \ 
--with-http_gzip_static_module \ 
--with-stream 
# 编译安装 
make && make install

```

### wget

文件下载

语法：wget [option]... [URL]...

### tar

打包/解压文件

- **-c** ：新建打包文件
- **-x** ：解压文件，配合-C解压到对应的文件目录
- **-f ** ：压缩或解压时指定要处理的文件
- **-j**  ：通过bzip2方式压缩或解压，最后以tar.br2为后缀，压缩后大小小于.tar.gz
- **-z** ：通过gzip方式压缩或解压，最后以.tar.gz为后缀
- **-v** ：显示操作过程
- **-t** ：查看打包文件中的内容
- **-C** dir ：指定压缩/解压缩的目录`dir`，若未指定，默认为当前目录

`tar -xf nginx-1.16.0.tar.gz`就是解压文件到当前目录

### cd

切换目录

语法：cd [参数] 目录

- **~** ：用户家目录
- **.** ：当前目录
- **..** ：当前目录的上一级目录
- **/** ：根目录
- **-** ：上一次所在目录

以`/`开头的为绝对路径，反之为相对路径。

### 配置(configure)

源码的安装一般由三个步骤组成：**配置(configure)、编译(make)、安装(make install)**。

Configure是一个可执行脚本，它有很多选项，在待安装的源码路径下使用命令./configure  --help输出详细的选项列表。

`./configure` 就是执行脚本，而`./configure \ `代表要进行一系列配置，如上所示，进行诸如安装路径、配置文件路径等的配置，每一条配置最后加上 `\`代表还没输入完，否在就直接执行`configure`脚本，完成配置。

- **--prefix**：代表安装路径，不指定则可执行文件默认放在`/usr/local/bin`下
- **--conf-path**：配置文件所在地，不指定默认在`usr/local/etc`下
- **--sbin-path**：超级二进制文件的安装位置.这是一些通常只能由超级用户执行的程序。
- **--with**：指定依赖

### 编译与安装

`make && make install`

## 配置

### 包含各系统的配置文件

修改`nginx.conf`，将`extra/cd`目录下的各个系统的配置文件包含进去。

### 设置开机自启

1. 新建 `/usr/lib/systemd/system/nginx.service` 文件，并输入以下内容

```bash
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target
 
[Service]
Type=forking
PIDFile=/var/run/nginx.pid
# Nginx will fail to start if /run/nginx.pid already exists but has the wrong
# SELinux context. This might happen when running `nginx -t` from the cmdline.
# https://bugzilla.redhat.com/show_bug.cgi?id=1268621
ExecStartPre=/usr/bin/rm -f /var/run/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true
 
[Install]
WantedBy=multi-user.target
```

2. 修改nginx.conf pid配置 和用户

```bash
user  root;
# pid文件生成位置与nginx.service的配置保持一致
pid   /var/run/nginx.pid;
```

3. 设置开机自启

```bash
systemctl  enable  nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
```

### 编辑文件内容

**vi/vim**：linux的文本编辑命令，`vim`是`vi`的加强版本，兼容vi的所有指令，不仅能编辑文本，还具有shell程序编辑功能，可以不同颜色的字体来辨别语法的正确性，极大方便了程序的设计和编辑性。

#### 进入文件:vi/vim  test.txt

#### 移动光标

- **[Ctrl] + [f]**：向下移动一页
- **[Ctrl] + [b]**：向上移动一页
- **[PageUp]**：向上移动一页
- **[PageDn]**：向上移动一页
- **[0]**：移动到本行第一个字符处
- **[$]**：移动到本行最后一个字符处
- **[home]**：移动到本行第一个字符处
- **[end]**：移动到本行最后一个字符处
- **[g]**：移动到文件的第一个字符处
- **[G]**：移动到文件的最后一个字符处
- **[Ctrl] + [←/→方向键]**：向左或向右移动一个单词

删除、复制和粘贴

`vi/vim dir`默认进入一般模式，可以在键盘输入以下命令，实现删除、复制、粘贴

- x,X：在一行字中，x 为向后删除一个字符（相当于[Del]键），X 为向前删除一个字符（相当于[Backspace]）
- dd：删除光标所在的一整行
- [n]dd：删除光标所在的向下 n 行
- yy：复制光标所在的一行
- nyy：复制光标所在的向下 n 行
- p,P：p 为将已复制的内容在光标的下一行粘贴，P 则为粘贴在光标的上一行

#### 插入模式

在一般模式下按i、a、o进入，效果大体相同，区别只有光标位置

#### 编辑数据后切换到末行模式

在插入模式中，点ess退出，然后输入

- :w  将编辑的数据写入到硬盘中
- :q  不保存退出vi，若后面加上!，则为强制退出
- :wq  保存后退出vi，后面加!，为强制退出

### 设置开机自启

新建 `/usr/lib/systemd/system/nginx.service` 文件。并进行如下配置

```bash
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target
 
[Service]
Type=forking
PIDFile=/var/run/nginx.pid
# Nginx will fail to start if /run/nginx.pid already exists but has the wrong
# SELinux context. This might happen when running `nginx -t` from the cmdline.
# https://bugzilla.redhat.com/show_bug.cgi?id=1268621
ExecStartPre=/usr/bin/rm -f /var/run/nginx.pid
# ExecStartPre、ExecStart、ExecReload均改成自己nginx下载目录
ExecStartPre=/opt/apps/nginx/sbin/nginx -t
ExecStart=/opt/apps/nginx/sbin/nginx
ExecReload=/opt/apps/nginx/sbin/nginx -s reload
ExecStop=/opt/apps/nginx/sbin/nginx -s quit
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true
 
[Install]
WantedBy=multi-user.target
```

**对应key说明：**

- Description:描述服务
- After:描述服务类别
- [Service]服务运行参数的设置
- Type=forking是后台运行的形式
- ExecStart为服务的具体运行命令
- ExecReload为重启命令
- ExecStop为停止命令
- PrivateTmp=True表示给服务分配独立的临时空间
- [Install]运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3

修改nginx.conf pid配置和用户配置 （在安装时设定的nginx配置目录中配置）

```bash
user  root;
# pid文件生成位置与nginx.service 保持一致
pid  /var/run/nginx.pid;
```

设置开机自启：

```bash
systemctl enable nginx
# 执行上述命令会自动创建软连接Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
```

### 启动与关闭

- systemctl start nginx.service　（启动nginx服务）
- systemctl stop nginx.service　（停止nginx服务）
- systemctl enable nginx.service （设置开机自启动）
- systemctl disable nginx.service （停止开机自启动）
- systemctl status nginx.service （查看服务当前状态）
- systemctl restart nginx.service　（重新启动服务）
- systemctl list-units --type=service （查看所有已启动的服务）