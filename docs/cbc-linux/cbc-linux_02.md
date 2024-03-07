# Linux 文件管理

Linux 中的所有数据都被保存在文件中，所有的文件被分配到不同的目录。目录是一种类似于树的结构，称为文件系统。

当你使用 Linux 时，大部分时间都会和文件打交道，通过本节可以了解基本的文件操作，如创建文件、删除文件、复制文件、重命名文件以及为文件创建链接等。

在 Linux 中，有三种基本的文件类型：

#### 1) 普通文件

普通文件是以字节为单位的数据流，包括文本文件、源码文件、可执行文件等。文本和二进制对 Linux 来说并无区别，对普通文件的解释由处理该文件的应用程序进行。

#### 2) 目录

目录可以包含普通文件和特殊文件，目录相当于 Windows 和 Mac OS 中的文件夹。

#### 3) 设备文件

有些教程中称特殊文件，是一个含义。Linux 与外部设备（例如光驱，打印机，终端，modern 等）是通过一种被称为设备文件的文件来进行通信。Linux 输入输出到外部设备的方式和输入输出到一个文件的方式是相同的。Linux 和一个外部设备通讯之前，这个设备必须首先要有一个设备文件存在。

例如，每一个终端都有自己的设备文件来供 Linux 写数据（出现在终端屏幕上）和读取数据（用户通过键盘输入）。

设备文件和普通文件不一样，设备文件中并不包含任何数据。

设备文件有两种类型：字符设备文件和块设备文件。

*   字符设备文件以字母"c"开头。字符设备文件向设备传送数据时，一次传送一个字符。典型的通过字符传送数据的设备有终端、打印机、绘图仪、modern 等。字符设备文件有时也被称为"raw"设备文件。
*   块设备文件以字母"b"开头。块设备文件向设备传送数据时，先从内存中的 buffer 中读或写数据，而不是直接传送数据到物理磁盘。磁盘和 CD-ROMS 既可以使用字符设备文件也可以使用块设备文件。

## 查看文件

查看当前目录下的文件和目录可以使用 **ls** 命令，例如：

```
$ls

bin        hosts  lib     res.03
ch07       hw1    pub     test_results
ch07.bak   hw2    res.01  users
docs       hw3    res.02  work
```

通过 **ls** 命令的 **-l** 选项，你可以获取更多文件信息，例如：

```
$ls -l
total 1962188

drwxrwxr-x  2 amrood amrood      4096 Dec 25 09:59 uml
-rw-rw-r--  1 amrood amrood      5341 Dec 25 08:38 uml.jpg
drwxr-xr-x  2 amrood amrood      4096 Feb 15  2006 univ
drwxr-xr-x  2 root   root        4096 Dec  9  2007 urlspedia
-rw-r--r--  1 root   root      276480 Dec  9  2007 urlspedia.tar
drwxr-xr-x  8 root   root        4096 Nov 25  2007 usr
drwxr-xr-x  2    200    300      4096 Nov 25  2007 webthumb-1.01
-rwxr-xr-x  1 root   root        3192 Nov 25  2007 webthumb.php
-rw-rw-r--  1 amrood amrood     20480 Nov 25  2007 webthumb.tar
-rw-rw-r--  1 amrood amrood      5654 Aug  9  2007 yourfile.mid
-rw-rw-r--  1 amrood amrood    166255 Aug  9  2007 yourfile.swf
drwxr-xr-x 11 amrood amrood      4096 May 29  2007 zlib-1.2.3
$
```

每一列的含义如下：

*   第一列：文件类型。
*   第二列：表示文件个数。如果是文件，那么就是 1；如果是目录，那么就是该目录中文件的数目。
*   第三列：文件的所有者，即文件的创建者。
*   第四列：文件所有者所在的用户组。在 Linux 中，每个用户都隶属于一个用户组。
*   第五列：文件大小（以字节计）。
*   第六列：文件被创建或上次被修改的时间。
*   第七列：文件名或目录名。

注意：每一个目录都有一个指向它本身的子目录"." 和指向它上级目录的子目录".."，所以对于一个空目录，第二列应该为 2。

通过 **ls -l** 列出的文件，每一行都是以 a、d、- 或 l 开头，这些字符表示文件类型：

| 前缀 | 描述 |
| - | 普通文件。如文本文件、二进制可执行文件、源代码等。 |
| b | 块设备文件。硬盘可以使用块设备文件。 |
| c | 字符设备文件。硬盘也可以使用字符设备文件。 |
| d | 目录文件。目录可以包含文件和其他目录。 |
| l | 符号链接（软链接）。可以链接任何普通文件，类似于 Windows 中的快捷方式。 |
| p | 具名管道。管道是进程间的一种通信机制。 |
| s | 用于进程间通信的套接字。 |

提示：通俗的讲软连接就是 windows 的快捷方式，原来文件删了，快捷方式虽然在但是不起作用了。

## 元字符

元字符是具有特殊含义的字符。* 和 ? 都是元字符：

*   * 可以匹配 0 个或多个任意字符；
*   ? 匹配一个字符。

例如

```
$ls ch*.doc
```

可以显示所有以 ch 开头，以 .doc 结尾的文件：

```
ch01-1.doc   ch010.doc  ch02.doc    ch03-2.doc
ch04-1.doc   ch040.doc  ch05.doc    ch06-2.doc
ch01-2.doc ch02-1.doc c
```

这里，* 匹配任意一个字符。如果你希望显示所有以 .doc 结尾的文件，可以使用

```
$ls *.doc。
```

## 隐藏文件

隐藏文件的第一个字符为英文句号或点号(.)，Linux 程序（包括 Shell）通常使用隐藏文件来保存配置信息。

下面是一些常见的隐藏文件：

.profile：Bourne shell (sh) 初始化脚本

.kshrc：Korn shell (ksh) 初始化脚本

.cshrc：C shell (csh) 初始化脚本

.rhosts：Remote shell (rsh) 配置文件

查看隐藏文件需要使用 **ls** 命令的 **-a** 选项：

```
$ ls -a

.         .profile       docs     lib     test_results
..        .rhosts        hosts    pub     users
.emacs    bin            hw1      res.01  work
.exrc     ch07           hw2      res.02
.kshrc    ch07.bak       hw3      res.03
$
```

一个点号(.)表示当前目录，两个点号(..)表示上级目录

注意：输入密码时，星号(*)作为占位符，代表你输入的字符个数。

## 创建文件

在 Linux 中，可以使用 vi 编辑器创建一个文本文件，例如：

```
$ vi filename
```

上面的命令会创建文件 filename 并打开，按下 i 键即可进入编辑模式，你可以向文件中写入内容。例如：

```
This is Linux file....I created it for the first time.....
I'm going to save this content in this file.
```

完成编辑后，可以按 esc 键退出编辑模式，也可以按组合键 Shift + ZZ 完全退出文件。这样，就完成了文件的创建。

```
$ vi filename
$
```

## 编辑文件

vi 编辑器可以用来编辑文件。由于篇幅限制，这里仅作简单介绍，将在后面章节进行详细讲解。

如下可以打开一个名为 filename 的文件：

```
$ vi filename
```

当文件被打开后，可以按 i 键进入编辑模式，按照自己的方式编辑文件。如果想移动光标，必须先按 esc 键退出编辑模式，然后使用下面的按键在文件内移动光标：

*   l 键向右移动
*   h 键向左移动
*   k 键向上移动
*   j 键向下移动

使用上面的按键，可以将光标快速定位到你想编辑的地方。定位好光标后，按 i 键再次进入编辑模式。编辑完成后按 esc 键退出编辑模式或者按组合键 Shift+ZZ 退出当前文件。

## 查看文件内容

可以使用 **cat** 命令来查看文件内容，下面是一个简单的例子：

```
$ cat filename
This is Linux file....I created it for the first time.....
I'm going to save this content in this file.
$
```

可以通过 **cat** 命令的 **-b** 选项来显示行号，例如：

```
$ cat -b filename
1   This is Linux file....I created it for the first time.....
2   I'm going to save this content in this file.
$
```

## 统计单词数目

可以使用 **wc** 命令来统计当前文件的行数、单词数和字符数，下面是一个简单的例子：

```
$ wc filename
2  19 103 filename
$
```

每一列的含义如下：

*   第一列：文件的总行数
*   第二列：单词数目
*   第三列：文件的字节数，即文件的大小
*   第四列：文件名

也可以一次查看多个文件的内容，例如：

```
$ wc filename1 filename2 filename3
```

## 复制文件

可以使用 **cp** 命令来复制文件。**cp** 命令的基本语法如下：

```
$ cp source_file destination_file
```

下面的例子将会复制 filename 文件：

```
$ cp filename copyfile
$
```

现在在当前目录中会多出一个和 filename 一模一样的 copyfile 文件。

## 重命名文件

重命名文件可以使用 **mv** 命令，语法为：

```
$ mv old_file new_file
```

下面的例子将会把 filename 文件重命名为 newfile：

```
$ mv filename newfile
$
```

现在在当前目录下，只有一个 newfile 文件。

**mv** 命令其实是一个移动文件的命令，不但可以更改文件的路径，也可以更改文件名。

## 删除文件

**rm**命令可以删除文件，语法为：

```
$ rm filename
```

注意：删除文件是一种危险的行为，因为文件内可能包含有用信息，建议结合 **-i** 选项来使用 **rm** 命令。

下面的例子会彻底删除一个文件：

```
$ rm filename
$
```

你也可以一次删除多个文件：

```
$ rm filename1 filename2 filename3
$
```

## 标准的 Linux 流

一般情况下，每个 Linux 程序运行时都会创建三个文件流（三个文件）：

*   标准输入流(stdin)：stdin 的文件描述符为 0，Linux 程序默认从 stdin 读取数据。
*   标准输出流(stdout)：stdout 的文件描述符为 1，Linux 程序默认向 stdout 输出数据。
*   标准错误流(stderr)：stderr 的文件描述符为 2，Linux 程序会向 stderr 流中写入错误信息。