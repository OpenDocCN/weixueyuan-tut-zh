# Linux 简介

## Linux 简介

严格的来讲，Linux 不算是一个操作系统，只是一个 Linux 系统中的**内核**，即计算机软件与硬件通讯之间的平台；Linux 的全称是 GNU/Linux，这才算是一个真正意义上的 Linux 系统。GNU 是 Richard Stallman 组织的一个项目，世界各地的程序员可以变形 GNU 程序，同时遵循 GPL 协议，允许任何人任意改动。但是，修改后的程序必须遵循 GPL 协议。

Linux 是一个多用户多任务的操作系统，也是一款自由软件，完全兼容 POSIX 标准，拥有良好的用户界面，支持多种处理器架构，移植方便。

为程序分配系统资源，处理计算机内部细节的软件叫做操作系统或者内核。如果你希望详细了解操作系统的概念，请查看操作系统教程。

用户通过 Shell 与 Linux 内核交互。Shell 是一个命令行解释工具（是一个软件），它将用户输入的命令转换为内核能够理解的语言（命令）。

Linux 下，很多工作都是通过命令完成的，学好 Linux，首先要掌握常用命令。

## Linux 版本

内核版本指的是在 Linus 领导下的开发小组开发出的系统内核的版本号。Linux 的每个内核版本使用形式为 x.y.zz-www 的一组数字来表示。其中：

*   x.y：为 linux 的主版本号。通常 y 若为奇数，表示此版本为测试版，系统会有较多 bug，主要用途是提供给用户测试。
*   zz：为次版本号。
*   www：代表发行号（注意，它与发行版本号无关）。

当内核功能有一个飞跃时，主版本号升级，如 Kernel2.2、2.4、2.6 等。如果内核增加了少量补丁时，常常会升级次版本号，如 Kernel2.6.15、2.6.20 等。

一些组织或厂家将 Linux 内核与 GNU 软件（系统软件和工具）整合起来，并提供一些安装界面和系统设定与管理工具，这样就构成了一个发型套件，例如 Ubuntu、Red Hat、Centos、Fedora、SUSE、Debian、FreeBSD 等。相对于内核版本，发行套件的版本号随着发布者的不同而不同，与系统内核的版本号是相对独立的。因此把 Red Hat 等直接说成是 Linux 是不确切的，它们是 Linux 的发行版本，更确切地说，应该叫做“以 linux 为核心的操作系统软件包”。

## Linux 体系结构

下面是 Linux 体系结构的示意图：![](img/c95e79fb4f22139f0b9a9d3252c9c3cb.jpg)

在所有 Linux 版本中，都会涉及到以下几个重要概念：

*   内核：内核是操作系统的核心。内核直接与硬件交互，并处理大部分较低层的任务，如内存管理、进程调度、文件管理等。
*   Shell：Shell 是一个处理用户请求的工具，它负责解释用户输入的命令，调用用户希望使用的程序。
*   命令和工具：日常工作中，你会用到很多系统命令和工具，如 cp、mv、cat 和 grep 等。在 Linux 系统中，有 250 多个命令，每个命令都有多个选项；第三方工具也有很多，他们也扮演着重要角色。
*   文件和目录：Linux 系统中所有的数据都被存储到文件中，这些文件被分配到各个目录，构成文件系统。Linux 的目录与 Windows 的文件夹是类似的概念。

## 系统启动（开机）

如果你有一台装有 Linux 的电脑，加电后系统会自动启动，然后提示你登录系统，只有登录后才能进行其他操作。

## 登录 Linux

第一次使用 Linux，会看到登录的提示，如下所示：

```
login:
```

#### 登录步骤：

*   登录 Linux 必须有用户名（用户 ID）和密码，如果没有，可以向管理员所要。
*   在登录提示处输入用户名并回车；用户名是区分大小写的，输入时要注意。
*   然后会提示你输入密码，密码也是区分大小写的。
*   如果用户名和密码正确，那么会成功登录，并看到上次登录信息。

```
login : amrood
amrood's password:
Last login: Sun Jun 14 09:32:32 2009 from 62.61.164.73
$
```

登录后会出现命令提示符($)，你可以输入任何命令。下面通过 cal 命令来查看日历：

```
$ cal
     June 2009
Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6
7   8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30

$
```

## 修改密码

Linux 系统通过密码来保证数据和文件的安全，防止黑客破解和攻击。你可以通过以下方法来修改密码：

*   输入 password 命令。
*   输入你现在使用的密码。
*   输入新密码。注意密码不要过于简单，简单的密码往往会为入侵者大开便利之门。
*   确认密码，再输入一遍刚才的密码。

```
$ passwd
Changing password for amrood
(current) Linux password:******
New Linux password:*******
Retype new Linux password:*******
passwd: all authentication tokens updated  successfully

$
```

注意：输入的密码是看不到的，只会看到一个占位符(*)。

## 查看目录和文件

在 Linux 中，所有的数据都被保存在文件中，所有的文件又被分配到不同的目录；目录是一种类似树的结构，称为文件系统。

你可以使用 ls 命令来查看当前目录下的文件和目录。下面的例子，使用了 ls 命令的 -l 选项：

```
$ ls -l
total 19621
drwxrwxr-x  2 amrood amrood      4096 Dec 25 09:59 uml
-rw-rw-r--  1 amrood amrood      5341 Dec 25 08:38 uml.jpg
drwxr-xr-x  2 amrood amrood      4096 Feb 15  2006 univ
drwxr-xr-x  2 root   root        4096 Dec  9  2007 urlspedia
-rw-r--r--  1 root   root      276480 Dec  9  2007 urlspedia.tar
drwxr-xr-x  8 root   root        4096 Nov 25  2007 usr
-rwxr-xr-x  1 root   root        3192 Nov 25  2007 webthumb.php
-rw-rw-r--  1 amrood amrood     20480 Nov 25  2007 webthumb.tar
-rw-rw-r--  1 amrood amrood      5654 Aug  9  2007 yourfile.mid
-rw-rw-r--  1 amrood amrood    166255 Aug  9  2007 yourfile.swf

$
```

注意：以 d* 开头的为目录，如 uml、univ、urlspedia 等；其他的都是文件。

## 查看当前用户信息

登录系统后，如果你希望知道自己的用户名（用户 ID），可以使用 whoami 命令：

```
$ whoami
amrood

$
```

如果你希望了解更多关于当前用户的信息，可以使用 who am i 命令，读者可以自己尝试一下。

## 查看当前在线用户

如果你希望知道当前在线的用户（同时登录到系统的用户），可以使用 users、who 和 w 命令：

```
$ users
amrood bablu qadir

$ who
amrood ttyp0 Oct 8 14:10 (limbo)
bablu  ttyp2 Oct 4 09:08 (calliope)
qadir  ttyp4 Oct 8 12:09 (dent)

$
```

w 命令可以看到在线用户的更多信息，读者可以自己尝试。

## 退出登录

完成工作后，你需要退出系统，防止他人使用你的账户。

使用 logout 命令即可退出登录，系统会清理有关信息并断开连接。

## 关闭系统（关机）

关系 Linux 系统可以使用下列命令：

| 命令 | 说明 |
| halt | 直接关闭系统 |
| init 0 | 使用预先定义的脚本关闭系统，关闭前可以清理和更新有关信息 |
| init 6 | 重新启动系统 |
| poweroff | 通过断电来关闭系统 |
| reboot | 重新启动系统 |
| shutdown | 安全关闭系统 |

注意：一般情况下只有超级用户和 root 用户（Linux 系统中的最高特权用户）才有关闭系统的权限，但是给普通用户赋予相应权限也可以关闭系统。