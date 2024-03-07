# Linux 目录

目录也是一个文件，它的唯一功能是用来保存文件及其相关信息。所有的文件，包括普通文件、设备文件和目录文件，都会被保存到目录中。

## 主目录

登录后，你所在的位置就是你的主目录（或登录目录），接下来你主要是在这个目录下进行操作，如创建文件、删除文件等。

使用下面的命令可以随时进入主目录：

```
$cd ~
$
```

这里 ~ 就表示主目录。如果你希望进入其他用户的主目录，可以使用下面的命令：

```
$cd ~username
$
```

返回进入当前目录前所在的目录可以使用下面的命令：

```
$cd -
$
```

## 绝对路径和相对路径

Linux 的目录有清晰的层次结构，/ 代表根目录，所有的目录都位于 / 下面；文件在层次结构中的位置可以用路径来表示。

如果一个路径以 / 开头，就称为绝对路径；它表示当前文件与根目录的关系。举例如下：

```
/etc/passwd
/users/sjones/chem/notes
/dev/rdsk/Os3
```

不以 / 开头的路径称为相对路径，它表示文件与当前目录的关系。例如：

```
chem/notes
personal/res
```

获取当前所在的目录可以使用 pwd 命令：

```
$pwd
/user0/home/amrood

$
```

查看目录中的文件可以使用 **ls** 命令：

```
$ls dirname
```

下面的例子将遍历 /usr/local 目录下的文件：

```
$ls /usr/local

X11       bin          gimp       jikes       sbin
ace       doc          include    lib         share
atalk     etc          info       man         ami
```

## 创建目录

可以使用 **mkdir** 命令来创建目录，语法为：

```
$mkdir dirname
```

dirname 可以为绝对路径，也可以为相对路径。例如

```
$mkdir mydir
$
```

会在当前目录下创建 mydir 目录。又如

```
$mkdir /tmp/test-dir
$
```

会在 /tmp 目录下创建 test-dir 目录。**mkdir** 成功创建目录后不会输出任何信息。

也可以使用 **mkdir** 命令同时创建多个目录，例如

```
$mkdir docs pub
$
```

会在当前目录下创建 docs 和 pub 两个目录。

## 创建父目录

使用 **mkdir** 命令创建目录时，如果上级目录不存在，就会报错。下面的例子中，mkdir 会输出错误信息：

```
$mkdir /tmp/amrood/test
mkdir: Failed to make directory "/tmp/amrood/test";
No such file or directory
$
```

为 **mkdir** 命令增加 **-p** 选项，可以一级一级创建所需要的目录，即使上级目录不存在也不会报错。例如

```
$mkdir -p /tmp/amrood/test
$
```

会创建所有不存在的上级目录。

## 删除目录

可以使用 **rmdir** 命令来删除目录，例如：

```
$rmdir dirname
$
```

注意：删除目录时请确保目录为空，不会包含其他文件或目录。

也可以使用 **rmdir** 命令同时删除多个目录：

```
$rmdir dirname1 dirname2 dirname3
$
```

如果 dirname1、dirname2、dirname3 为空，就会被删除。**rmdir** 成功删除目录后不会输出任何信息。

## 改变所在目录

可以使用 **cd** 命令来改变当前所在目录，进入任何有权限的目录，语法为：

```
$cd dirname

```

dirname 为路径，可以为相对路径，也可以为绝对路径。例如

```
$cd /usr/local/bin
$
```

可以进入 /usr/local/bin 目录。可以使用相对路径从这个目录进入 /usr/home/amrood 目录：

```
$cd ../../home/amrood
$
```

## 重命名目录

**mv** (move) 命令也可以用来重命名目录，语法为：

```
$mv olddir newdir
```

下面的例子将会把 mydir 目录重命名为 yourdir 目录：

```
$mv mydir yourdir
$
```

## 点号(.)

一个点号(.)表示当前目录，两个点号(..)表示上级目录（父目录）。

**ls** 命令的 **-a** 选项可以查看所有文件，包括隐藏文件；**-l** 选项可以查看文件的所有信息，共有 7 列。例如：

```
$ls -la
drwxrwxr-x    4    teacher   class   2048  Jul 16 17.56 .
drwxr-xr-x    60   root              1536  Jul 13 14:18 ..
----------    1    teacher   class   4210  May 1 08:27 .profile
-rwxr-xr-x    1    teacher   class   1948  May 12 13:42 memo
$
```