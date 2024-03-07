# Linux 文件权限和访问模式

为了更加安全的存储文件，Linux 为不同的文件赋予了不同的权限，每个文件都拥有下面三种权限：

*   所有者权限：文件所有者能够进行的操作
*   组权限：文件所属用户组能够进行的操作
*   外部权限（其他权限）：其他用户可以进行的操作。

## 查看文件权限

使用 **ls -l** 命令可以查看与文件权限相关的信息：

```
$ls -l /home/amrood
-rwxr-xr--  1 amrood   users 1024  Nov 2 00:10  myfile
drwxr-xr--- 1 amrood   users 1024  Nov 2 00:10  mydir
```

第一列就包含了文件或目录的权限。

第一列的字符可以分为三组，每一组有三个，每个字符都代表不同的权限，分别为读取(r)、写入(w)和执行(x)：

*   第一组字符(2-4)表示文件所有者的权限，-rwxr-xr-- 表示所有者拥有读取(r)、写入(w)和执行(x)的权限。
*   第二组字符(5-7)表示文件所属用户组的权限，-rwxr-xr-- 表示该组拥有读取(r)和执行(x)的权限，但没有写入权限。
*   第三组字符(8-10)表示所有其他用户的权限，rwxr-xr-- 表示其他用户只能读取(r)文件。

## 文件访问模式

文件权限是 Linux 系统的第一道安全防线，基本的权限有读取(r)、写入(w)和执行(x)：

*   读取：用户能够读取文件信息，查看文件内容。
*   写入：用户可以编辑文件，可以向文件写入内容，也可以删除文件内容。
*   执行：用户可以将文件作为程序来运行。

## 目录访问模式

目录的访问模式和文件类似，但是稍有不同：

*   读取：用户可以查看目录中的文件
*   写入：用户可以在当前目录中删除文件或创建文件
*   执行：执行权限赋予用户遍历目录的权利，例如执行 cd 和 ls 命令。

## 改变权限

可以使用 **chmod** (change mode) 命令来改变文件或目录的访问权限，权限可以使用符号或数字来表示。

#### 使用符号表示权限

对于初学者来说最简单的就是使用符号来改变文件或目录的权限，你可以增加(+)和删除(-)权限，也可以指定特定权限。

| 符号 | 说明 |
| + | 为文件或目录增加权限 |
| - | 删除文件或目录的权限 |
| = | 设置指定的权限 |

下面的例子将会修改 testfile 文件的权限：

```
$ls -l testfile
-rwxrwxr--  1 amrood   users 1024  Nov 2 00:10  testfile
$chmod o+wx testfile
$ls -l testfile
-rwxrwxrwx  1 amrood   users 1024  Nov 2 00:10  testfile
$chmod u-x testfile
$ls -l testfile
-rw-rwxrwx  1 amrood   users 1024  Nov 2 00:10  testfile
$chmod g=rx testfile
$ls -l testfile
-rw-r-xrwx  1 amrood   users 1024  Nov 2 00:10  testfile
```

也可以同时使用多个符号：

```
$chmod o+wx,u-x,g=rx testfile
$ls -l testfile
-rw-r-xrwx  1 amrood   users 1024  Nov 2 00:10  testfile
```

#### 使用数字表示权限

除了符号，也可以使用八进制数字来指定具体权限，如下表所示：

| 数字 | 说明 | 权限 |
| 0 | 没有任何权限 | --- |
| 1 | 执行权限 | --x |
| 2 | 写入权限 | -w- |
| 3 | 执行权限和写入权限：1 (执行) + 2 (写入) = 3 | -wx |
| 4 | 读取权限 | r-- |
| 5 | 读取和执行权限：4 (读取) + 1 (执行) = 5 | r-x |
| 6 | 读取和写入权限：4 (读取) + 2 (写入) = 6 | rw- |
| 7 | 所有权限: 4 (读取) + 2 (写入) + 1 (执行) = 7 | rwx |

下面的例子，首先使用 **ls -1** 命令查看 testfile 文件的权限，然后使用 **chmod** 命令更改权限：

```
$ls -l testfile
-rwxrwxr--  1 amrood   users 1024  Nov 2 00:10  testfile
$ chmod 755 testfile
$ls -l testfile
-rwxr-xr-x  1 amrood   users 1024  Nov 2 00:10  testfile
$chmod 743 testfile
$ls -l testfile
-rwxr---wx  1 amrood   users 1024  Nov 2 00:10  testfile
$chmod 043 testfile
$ls -l testfile
----r---wx  1 amrood   users 1024  Nov 2 00:10  testfile
```

## 更改所有者和用户组

在 Linux 中，每添加一个新用户，就会为它分配一个用户 ID 和群组 ID，上面提到的文件权限也是基于用户和群组来分配的。

有两个命令可以改变文件的所有者或群组：

*   **chown**：chown 命令是"change owner"的缩写，用来改变文件的所有者。
*   **chgrp**：chgrp 命令是"change group"的缩写，用来改变文件所在的群组。

**chown** 命令用来更改文件所有者，其语法如下：

```
$ chown user filelist
```

user 可以是用户名或用户 ID，例如

```
$ chown amrood testfile
$
```

将 testfile 文件的所有者改为 amrood。

注意：超级用户 root 可以不受限制的更改文件的所有者和用户组，但是普通用户只能更改所有者是自己的文件或目录。

**chgrp** 命令用来改变文件所属群组，其语法为：

```
$ chgrp group filelist
```

group 可以是群组名或群组 ID，例如

```
$ chgrp special testfile
$
```

将文件 testfile 的群组改为 special。

## SUID 和 SGID 位

在 Linux 中，一些程序需要特殊权限才能完成用户指定的操作。

例如，用户的密码保存在 /etc/shadow 文件中，出于安全考虑，一般用户没有读取和写入的权限。但是当我们使用 **passwd** 命令来更改密码时，需要对 /etc/shadow 文件有写入权限。这就意味着，passwd 程序必须要给我们一些特殊权限，才可以向 /etc/shadow 文件写入内容。

Linux 通过给程序设置 SUID(Set User ID)和 SGID(Set Group ID)位来赋予普通用户特殊权限。当我们运行一个带有 SUID 位的程序时，就会继承该程序所有者的权限；如果程序不带 SUID 位，则会根据程序使用者的权限来运行。

SGID 也是一样。一般情况下程序会根据你的组权限来运行，但是给程序设置 SGID 后，就会根据程序所在组的组权限运行。

如果程序设置了 SUID 位，就会在表示文件所有者可执行权限的位置上出现's'字母；同样，如果设置了 SGID，就会在表示文件群组可执行权限的位置上出现's'字母。如下所示：

```
$ ls -l /usr/bin/passwd
-r-sr-xr-x  1   root   bin  19031 Feb 7 13:47  /usr/bin/passwd*
$
```

上面第一列第四个字符不是'x'或'-'，而是's'，说明 /usr/bin/passwd 文件设置了 SUID 位，这时普通用户会以 root 用户的权限来执行 passwd 程序。

注意：小写字母's'说明文件所有者有执行权限(x)，大写字母'S'说明程序所有者没有执行权限(x)。

如果在表示群组权限的位置上出现 SGID 位，那么也仅有三类用户可以删除该目录下的文件：目录所有者、文件所有者、超级用户 root。

为一个目录设置 SUID 和 SGID 位可以使用下面的命令：

```
$ chmod ug+s dirname
$ ls -l
drwsr-sr-x 2 root root  4096 Jun 19 06:45 dirname
$
```