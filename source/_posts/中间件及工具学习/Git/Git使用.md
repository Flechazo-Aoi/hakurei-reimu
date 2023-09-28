---
title: Git使用
categories: 中间件及工具学习
date: 2022-12-28  17:22:33
tags:
- mybatis
- 框架
---

## Git是什么？

- Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。
- Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。
- Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

### Git 与 SVN 区别

Git 不仅仅是个版本控制系统，它也是个内容管理系统(CMS)，工作管理系统等。

Git 与 SVN 区别点：

- **Git 是分布式的，SVN 不是**：这是 Git 和其它非分布式的版本控制系统，例如 SVN，CVS 等，最核心的区别。
- **Git 把内容按元数据方式存储，而 SVN 是按文件：**所有的资源控制系统都是把文件的元信息隐藏在一个类似 .svn、.cvs 等的文件夹里。
- **Git 分支和 SVN 的分支不同：**分支在 SVN 中一点都不特别，其实它就是版本库中的另外一个目录。
- **Git 没有一个全局的版本号，而 SVN 有：**目前为止这是跟 SVN 相比 Git 缺少的最大的一个特征。
- **Git 的内容完整性要优于 SVN：**Git 的内容存储使用的是 SHA-1 哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

## Git配置

Git 提供了一个叫做 git config 的工具，专门用来配置或读取相应的工作环境变量。

这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

- `/etc/gitconfig` 文件：系统中对所有用户都普遍适用的配置。若使用 `git config` 时用 `--system` 选项，读写的就是这个文件。
- `~/.gitconfig` 文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用 `--global` 选项，读写的就是这个文件。
- 当前项目的 Git 目录中的配置文件（也就是工作目录中的 `.git/config` 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 `.git/config` 里的配置会覆盖 `/etc/gitconfig` 中的同名变量。

在 Windows 系统上，Git 会找寻用户主目录下的 .gitconfig 文件。主目录即 $HOME 变量指定的目录，一般都是 C:\Documents and Settings\$USER。

此外，Git 还会尝试找寻 /etc/gitconfig 文件，只不过看当初 Git 装在什么目录，就以此作为根目录来定位。

### 用户信息

配置个人的用户名称和电子邮件地址：

```bash
$ git config --global user.name "Flechazo-Aoi"
$ git config --global user.email 1664637635@qq.com
```

如果用了 **--global** 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

### 文本编辑器

设置Git默认使用的文本编辑器, 一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：

```bash
$ git config --global core.editor emacs
```

### 差异分析工具

还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：

```bash
$ git config --global merge.tool vimdiff
```

Git 可以理解 kdiff3，tkdiff，meld，xxdiff，emerge，vimdiff，gvimdiff，ecmerge，和 opendiff 等合并工具的输出信息。

### 查看配置信息

要检查已有的配置信息，可以使用 `git config --list` 命令，以此列出所有的配置信息

```bash
$ git config --list
init.defaultbranch=master
user.name=Flechazo-Aoi
user.email=1664637635@qq.com
```

也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可：

```bash
$ git config user.name
Flechazo-Aoi
```

有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig 和 ~/.gitconfig），不过最终 Git 实际采用的是最后一个。

这些配置我们也可以在 **~/.gitconfig** 或 **/etc/gitconfig** 看到，如下所示：

```bash
vim ~/.gitconfig 
```

来和修改显示配置文件相关信息。或者我们通过-e命令来编辑配置文件

```bash
$ git config -e    # 针对当前仓库 
$ git config -e --global   # 针对系统上所有仓库
```

git config的详细用法可以查看其usage。

## Git工作流程

一般工作流程如下：

- 克隆 Git 资源作为工作目录。
- 在克隆的资源上添加或修改文件。
- 如果其他人修改了，你可以更新资源。
- 在提交前查看修改。
- 提交修改。
- 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

下图展示了 Git 的工作流程：

![image-20230225222452030](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202302252224110.png)

## Git 工作区、暂存区和版本库

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫 stage 或 index。一般存放在 **.git** 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录 **.git**，这个不算工作区，而是 Git 的版本库。

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![image-20230225172930623](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202302251729763.png)

- 图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage/index），标记为 "master" 的是 master 分支所代表的目录树。
- 图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。
- 图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。
- 当对工作区修改（或新增）的文件执行 **git add** 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。
- 当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。
- 当执行 **git reset HEAD** 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
- 当执行 **git rm --cached <file>** 命令时，会直接从暂存区删除文件，工作区则不做出改变。
- 当执行 **git checkout .** 或者 **git checkout -- <file>** 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区中的改动。
- 当执行 **git checkout HEAD .** 或者 **git checkout HEAD <file>** 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。

## Git 创建仓库

可以使用一个已经存在的目录作为 Git 仓库

### git init

Git 使用 **git init** 命令来初始化一个 Git 仓库，**Git 的很多命令都需要在 Git 的仓库中运行**，所以 **git init** 是使用 Git 的第一个命令。

在执行完成 **git init** 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变。.git是隐藏项目，windows文件系统中默认是不显示的。

#### 使用方法

- 使用当前目录作为 Git 仓库，我们只需使它初始化。该命令执行完后会在当前目录生成一个 .git 目录。

```bash
$ git init
```

使用我们指定目录作为Git仓库。该命令执行完成之后会在当前目录下创建一个名为newrepo的目录，并在 newrepo 目录下生成一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

```bash
$ git init newrepo
```

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件(比如*,java)进行跟踪，然后提交：

```bash
$ git add *.java
$ git commit -m '初始化项目版本名称'
```

以上命令将目录下以 .java 结尾的文件提交到仓库中。

> **注：** 在 Linux 系统中，commit 信息使用单引号 **'**，Windows 系统，commit 信息使用双引号 **"**。
>
> 所以在 git bash 中 **git commit -m '提交说明'** 这样是可以的，在 Windows 命令行中就要使用双引号 **git commit -m "提交说明"**。

### git clone

我们使用 **git clone** 从现有 Git 仓库中拷贝项目,克隆仓库的命令格式为：

```bash
$ git clone <repo> <directory>
```

> **参数说明：**
>
> - **repo:**Git 仓库地址。
> - **directory:**本地目录(用于指定克隆到的目录)。

比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：

```bash
$ git clone git://github.com/schacon/grit.git
```

执行该命令后，会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

```bash
$ git clone git://github.com/schacon/grit.git mygit
```

## Git 基本操作

Git 的工作就是创建和保存项目的快照及与之后的快照进行对比。

Git 常用的是以下 6 个命令：**git clone**、**git push**、**git add** 、**git commit**、**git checkout**、**git pull**

![image-20230225200738676](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202302252007724.png)

### 创建仓库命令

#### git init

**git init** 命令用于在目录中创建新的 Git 仓库。

在目录中执行 **git init** 就可以创建一个 Git 仓库了。

#### git clone

**git clone** 用于从远程仓库拷贝一个 Git 仓库到本地，让自己能够查看该项目，或者进行修改。

格式如下，**[url]** 是你要拷贝的项目。

```bash
$  git clone [url]
```

拷贝完成后，在当前目录下会生成一个你拷贝的项目目录，并且恢复至该项目的全部记录，

默认情况下，Git 会按照你提供的 URL 所指向的项目的名称创建你的本地项目目录。 通常就是该 URL 最后一个 / 之后的项目名称。如果你想要一个不一样的名字， 你可以在该命令后加上你想要的名称，格式为：

```bash
$ git clone [url]  [project-name]
```

### 提交与修改

Git 的工作就是创建和保存项目的快照及与之后的快照进行对比。

#### git add

**git add** 命令可将该文件添加到暂存区。文件名称同样也支持通配符

```bash
$ git add [file1] [file2] ... # 添加一个或多个文件到暂存区：
$ git add [dir] # 添加指定目录到暂存区，包括子目录：
$ git add .    	# 添加当前目录下的所有文件到暂存区：
```

#### git status

git status 命令用于查看在你上次提交之后是否有对文件进行再次修改。

```bash
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   11.png
        new file:   "\345\244\232\347\233\212\347\275\221\347\273\234.pdf"
        new file:   "\345\244\232\347\233\212\347\275\221\347\273\234.png"
        modified:   "\350\265\265\347\204\223\350\266\205.pdf"
        new file:   "\351\235\222\345\262\233\351\274\216\344\277\241.pdf"
```

通常我们使用 **-s** 参数来获得简短的输出结果：

```bash
$ git status -s
A  11.png
A  "\345\244\232\347\233\212\347\275\221\347\273\234.pdf"
A  "\345\244\232\347\233\212\347\275\221\347\273\234.png"
M  "\350\265\265\347\204\223\350\266\205.pdf"
A  "\351\235\222\345\262\233\351\274\216\344\277\241.pdf"
```

A代表 added，M代表 modified

#### git diff

git diff 命令比较文件的不同，即比较文件在暂存区和工作区的差异。

git diff 命令显示已写入暂存区和已经被修改但尚未写入暂存区文件的区别。

显示暂存区和工作区的差异:

```bash
$ git diff [file]
```

显示暂存区和上一次提交(commit)的差异:

```bash
$ git diff --cached [file]
或
$ git diff --staged [file]
```

#### git commit

git commit 命令将暂存区内容添加到本地仓库中。

提交暂存区到本地仓库中: [message] 可以是一些备注信息。

```bash
git commit -m [message]

```

提交暂存区的指定文件到仓库区：

```bash
$ git commit [file1] [file2] ... -m [message]
```

**-a** 参数设置修改文件后不需要执行 git add 命令，直接来提交

```bash
$ git commit -a
```

##### 修改实例

接下来我们就可以对 hello.php 的所有改动从暂存区内容添加到本地仓库中。以下实例，我们使用 -m 选项以在命令行中提供提交注释。

```bash
$ git add hello.php
$ git status -s
A  README
A  hello.php
$ git commit -m '第一次版本提交'
[master (root-commit) d32cf1f] 第一次版本提交
 2 files changed, 4 insertions(+)
 create mode 100644 README
 create mode 100644 hello.php
```

现在我们已经记录了快照。如果我们再执行 git status:

```bash
$ git status
# On branch master
nothing to commit (working directory clean)
```

以上输出说明我们在最近一次提交之后，没有做任何改动，是一个 "working directory clean"，翻译过来就是干净的工作目录。

如果你没有设置 -m 选项，Git 会尝试为你打开一个编辑器以填写提交信息。 如果 Git 在你对它的配置中找不到相关信息，默认会打开 vim。

如果你觉得 git add 提交缓存的流程太过繁琐，Git 也允许你用 -a 选项跳过这一步。命令格式如下：

```bash
git commit -a
```

那么对工作去文件进行修改后可以直接执行以下命令，来达到从工作区到暂存区再到本地仓库的操作

```bash
$ git commit -am '修改 hello.php 文件'
```

#### git reset

git reset 命令用于回退版本，可以指定退回某一次提交的版本。

```bash
git reset [--soft | --mixed | --hard] [HEAD]
```

##### --mixed

默认选择参数，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。

```bash
git reset  [HEAD] 
```

```bash
$ git reset HEAD^            # 回退所有内容到上一个版本  
$ git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本  
$ git reset  052e           # 回退到指定版本
```

##### --soft

用于回退到某个版本：

```bash
git reset --soft HEAD
```

```bash
$ git reset --soft HEAD~3   # 回退上上上一个版本 
```

##### --hard

撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交：

```bash
git reset --hard HEAD
```

```bash
$ git reset --hard HEAD~3  # 回退上上上一个版本  
$ git reset --hard bae128  # 回退到某个版本回退点之前的所有信息。 
$ git reset --hard origin/master    # 将本地的状态回退到和远程仓库origin的master分支的一样
```

> **注意：**谨慎使用 **–-hard** 参数，它会删除回退点之前的所有信息。

##### HEAD 说明：

- HEAD 表示当前版本
- HEAD^ 上一个版本
- HEAD^^ 上上一个版本
- ...以此类推



可以使用 ～数字表示

- HEAD~0 表示当前版本
- HEAD~1 上一个版本
- HEAD^2 上上一个版本
- 以此类推

简而言之，执行 git reset HEAD 以取消之前 git add 添加，也就是不希望包含在下一提交快照中的缓存。

#### git rm

git rm 命令用于删除文件。

如果只是简单地从工作目录中手工删除文件，运行 **git status** 时就会在 **Changes not staged for commit** 的提示。

git rm 删除文件有以下几种形式：

将文件从暂存区和工作区中删除：

```bash
git rm <file>
```

如果此文件已经被修改过并且已经放到暂存区，则必须要用强制删除选项 **-f**。

```bash
git rm -f <file>
```

如果想把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 **--cached** 选项即可：

```bash
git rm --cached <file>
```

可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件：

```bash
git rm –r * 
```

#### git mv

git mv 命令用于移动或重命名一个文件、目录或软连接。此操作必须要在暂存区或者文件commit之后才能进行rename

```bash
git mv [file] [newfile]
```

如果新文件名已经存在，但还是要重命名它，可以使用 **-f** 参数：

```bash
git mv -f [file] [newfile]
```

我们可以添加一个 README 文件（如果没有的话）：

### 提交日志

Git 提交历史一般常用两个命令：

- **git log** - 查看历史提交记录。
- **git blame <file>** - 以列表形式查看指定文件的历史修改记录。

#### git log

在使用 Git 提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，我们可以使用 **git log** 命令查看。

我们可以用 --oneline 选项来查看历史记录的简洁的版本。

```bash
$ git log --oneline
$ git log --oneline
d5e9fc2 (HEAD -> master) Merge branch 'change_site'
c68142b 修改代码
7774248 (change_site) changed the runoob.php
c1501a2 removed test.txt、add runoob.php
3e92c19 add test.txt
3b58100 第一次版本提交
```

我们还可以用 --graph 选项，查看历史中什么时候出现了分支、合并。以下为相同的命令，开启了拓扑图选项：

```bash
*   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
|\  
| * 7774248 (change_site) changed the runoob.php
* | c68142b 修改代码
|/  
* c1501a2 removed test.txt、add runoob.php
* 3e92c19 add test.txt
* 3b58100 第一次版本提交
```

也可以用 **--reverse** 参数来逆向显示所有日志。

```bash
$ git log --reverse --oneline
3b58100 第一次版本提交
3e92c19 add test.txt
c1501a2 removed test.txt、add runoob.php
7774248 (change_site) changed the runoob.php
c68142b 修改代码
d5e9fc2 (HEAD -> master) Merge branch 'change_site'
```

如果只想查找指定用户的提交日志可以使用命令：git log --author , 例如，比方说我们要找 Git 源码中 Linus 提交最后5次的部分：

```bash
$ git log --author=Linus --oneline -5
```

如果要指定日期，可以执行几个选项：--since 和 --before，也可以用 --until 和 --after。

例如，如果我要看 Git 项目中三周前且在四月十八日之后的所有提交，我可以执行这个（我还用了 --no-merges 选项以隐藏合并提交）：

```bash
$ git log --oneline --before={3.weeks.ago} --after={2010-04-18} --no-merges
```

#### git blame

如果要查看指定文件的修改记录可以使用 git blame 命令，格式如下：

```bash
git blame <file>
```

- git blame 命令是以列表形式显示修改记录，如下实例：

- ```bash
  $ git blame README 
  ^d2097aa (tianqixin 2020-08-25 14:59:25 +0800 1) # Runoob Git 测试
  db9315b0 (runoob    2020-08-25 16:00:23 +0800 2) # 菜鸟教程 
  ```

### 远程操作

#### git remote

**git remote** 命令用于在远程仓库的操作。

显示所有远程仓库：

```bash
git remote -v
```

显示某个远程仓库的信息：

```bash
git remote show [remote]
```

添加远程版本库：

```bash
git remote add [shortname] [url]  # shortname为本地的版本库

# 提交到 Github
$ git remote add origin git@github.com:tianqixin/runoob-git-test.git
$ git push -u origin master
```

其他相关命令：

```bash
git remote rm name  # 删除远程仓库
git remote rename old_name new_name  # 修改仓库名
```

#### git fetch

**git fetch** 命令用于从远程获取代码库(从远程仓库获取代码到本地仓库)。

在执行 git fetch 之后紧接着执行 git merge 远程分支到你所在的任意分支。假设你配置好了一个远程仓库，并且你想要提取更新的数据，你可以首先执行:

```bash
git fetch [alias]
```

以上命令告诉 Git 去获取它有你没有的数据，也就是更新本地仓库使其与远程仓库一致，然后你可以执行：

```bash
git merge [alias]/[branch]
```

以上命令将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支。

#### git pull

**git pull** 命令用于从远程获取代码并合并本地的版本。

**git pull** 其实就是 **git fetch** 和 **git merge FETCH_HEAD** 的简写。

命令格式如下：

```bash
git pull <远程主机名>  <远程分支名>:<本地分支名>
```

将远程主机 origin 的 master 分支拉取过来，与本地的 brantest 分支合并。

```bash
git pull origin master:brantest
```

如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

```bash
git pull origin master
```

#### git push

**git push** 命令用于从将本地的分支版本上传到远程并合并。

命令格式如下：

```bash
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果本地分支名与远程分支名相同，则可以省略冒号：

```bash
git push <远程主机名> <本地分支名>
```

如果本地版本与远程版本有差异，但又要强制推送可以使用 --force 参数：

```bash
git push --force origin master
```

删除主机的分支可以使用 --delete 参数，以下命令表示删除 origin 主机的 master 分支：

```bash
git push origin --delete master
```

## Git 分支管理

几乎每一种版本控制系统都以某种形式支持分支，一个分支代表一条独立的开发线。使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。

Git 分支实际上是指向更改快照的指针。有人把 Git 的分支模型称为**必杀技特性**，而正是因为它，将 **Git** 从版本控制系统家族里区分出来。

### Git分支体系

#### master分支

master分支存放的是随时可供在**生产环境中部署的稳定版本代码。**master分支保存正式发布版本历史，tag标识不同的发布版本。一个项目只能有一个master分支。仅在发布新的正式版本时才更新master分支上的代码。每次更新master，都需按照《软件系统版本号命名指南》标记Tag，用于发布或回滚。master分支是保护分支，不可直接push到远程仓master分支。**master分支代码只能从release分支或hotfix分支合并过来。**

master主要用于发布阶段，对应版本为**正式版本**。

### Git分支相关命令

创建分支命令：

```bash
git branch (branchname)
```

切换分支命令:

```bash
git checkout (branchname)
```

当你切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录。

创建分支并切换到新创建的分支：

```bash
git checkout -b (branchname)
```

列出分支基本命令:

```bash
git branch  # 没有参数时，git branch 会列出你在本地的分支。
```

删除分支命令：

```bash
git branch -d (branchname)
```

合并分支命令:（branchname为你要把branchname分支合并到当前分支下）

```bash
git merge (branchname)
```

### 合并冲突

合并并不仅仅是简单的文件添加、移除的操作，Git 也会合并修改。

```bash
$ git branch
* master
$ cat test.php
```

首先，我们创建一个叫做 change 的分支，切换过去，并把 test.php 内容改为如下，并且提交到change分支中

```
<?php
echo 'test';
?>
```

在切换回master分支，master分支中test.php还为空，我们修改其内容为如下，并且提交到master分支中

```
<?php
echo 1;
?>
```

然后进行在master分支中合并change分支,如下

```bash
$ git merge change_site
Auto-merging test.php
CONFLICT (content): Merge conflict in test.php
Automatic merge failed; fix conflicts and then commit the result.
```

提示产生了冲突，一个合并冲突就产生了，合并冲突需要我们手动解决，解决完了之后，我们可以用 git add 要告诉 Git 文件冲突已经解决

## Git标签

如果你达到一个重要的阶段，并希望永远记住那个特别的提交快照，你可以使用 git tag 给它打上标签。

比如说，我们想为我们的 runoob 项目发布一个"1.0"版本。 我们可以用 git tag -a v1.0 命令给最新一次提交打上（HEAD）"v1.0"的标签。

-a 选项意为"创建一个带注解的标签"。 不用 -a 选项也可以执行的，但它不会记录这标签是啥时候打的，谁打的，也不会让你添加个标签的注解。

```bash
$ git tag -a v1.0 
```

当你执行 git tag -a 命令时，Git 会打开你的编辑器，让你写一句标签注解，就像你给提交写注解一样。

或者指定标签信息命令：

```bash
git tag -a <tagname> -m "runoob.com标签"
```

如果我们忘了给某个提交打标签，又将它发布了，我们可以给它追加标签。例如假设我们发布了提交 85fc7e7，但是那时候忘了给它打标签。 我们现在也可以：

```bash
$ git tag -a v0.9 85fc7e7
```

如果我们要查看所有标签可以使用以下命令：

```bash
$ git tag
v0.9
v1.0
```

删除标签

```bash
git tag -d v1.0
```

查看某个标签版本所修改的内容

```bash
git show v1.0
```

## Git 远程仓库(Github)

Git 并不像 SVN 那样有个中心服务器。

目前我们使用到的 Git 命令都是在本地执行，如果你想通过 Git 分享你的代码或者与其他开发人员合作。 你就需要将数据放到一台其他开发人员能够连接的服务器上。比如github，gitlab...

### 添加远程库

要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用,命令格式如下：

```bash
git remote add [shortname] [url]
```

由于你的本地 Git 仓库和 GitHub 仓库之间的传输是通过SSH加密的，所以我们需要配置验证信息：(只有首次需要)。使用以下命令生成 SSH Key：

```bash
$ ssh-keygen -t rsa -C "youremail@example.com"  # 你在 Github 上注册的邮箱
```

后面的 youremail@example.com为你在 Github 上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在 **~/** 下生成 **.ssh** 文件夹，进去，打开 **id_rsa.pub**，复制里面的 **key**。

```bash
$ ssh-keygen -t rsa -C "1664637635@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/hanser/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):    # 直接回车 就是不设置密码
Enter same passphrase again:                   # 直接回车 再次确认不设置密码
Your identification has been saved in /Users/tianqixin/.ssh/id_rsa.
Your public key has been saved in /Users/tianqixin/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:MDKVidPTDXIQoJwoqUmI4LBAsg5XByBlrOEzkxrwARI 1664637635@qq.com
The key's randomart image is:
+---[RSA 3072]----+
|E*+.+=**oo       |
|%Oo+oo=o. .      |
|%**.o.o.         |
|OO.  o o         |
|+o+     S        |
|.                |
|                 |
|                 |
|                 |
+----[SHA256]-----+
```

回到 github 上，进入 Account => Settings（账户配置）

![image-20230225221522586](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202302252215655.png)

左边选择 **SSH and GPG keys**，然后点击 **New SSH key** 按钮,title 设置标题，可以随便填，粘贴在你电脑上id_rsa.pub里生成的 key。

![image-20230225221620230](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202302252216354.png)

为了验证是否成功，输入以下命令

```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (52.74.223.119)' can't be established.
RSA key fingerprint is SHA256:
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes                   # 输入 yes
Warning: Permanently added 'github.com,52.74.223.119' (RSA) to the list of known hosts.
Hi Flechazo-Aoi! You've successfully authenticated, but GitHub does not provide shell access. # 成功信息
```

之后关于从远程仓库拉取以及上传到远程仓库的相关操作，参考基本操作一节