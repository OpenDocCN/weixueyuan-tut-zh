# GDB 下载和安装教程

> 原文：[`c.biancheng.net/view/8130.html`](http://c.biancheng.net/view/8130.html)

基于 Linux 系统的免费、开源，衍生出了多个不同的 Linux 版本，比如 Redhat、CentOS、Ubuntu、Debian 等。这些 Linux 发行版中，有些默认安装有 GDB 调试器，但有些默认不安装。

判断当前 Linux 发行版是否安装有 GDB 的方法也很简单，就是在命令行窗口中执行 gdb -v 命令。以本机安装的 CentOS 系统为例：

[root@bogon ~]# gdb -v
bash: gdb: command not found

如上所示，执行结果为“command not found”，表明当前系统中未安装 GDB 调试器。反之，若执行结果为：

[root@bogon ~]# gdb -v
GNU gdb (GDB) Red Hat Enterprise Linux (7.2-92.el6)
Copyright (C) 2010 Free Software Foundation, Inc.
....... <-省略部分信息

则表明当前系统安装了 GDB 调试器。

对于尚未安装 GDB 的 Linux 发行版，安装方法通常有以下 2 种：

1.  直接调用该操作系统内拥有的 GDB 安装包，使用包管理器进行安装。此安装方式的好处是速度快，但通常情况下安装的并非 GDB 的最新版本；
2.  前往 GDB 官网下载源码包，在本机编译安装。此安装方式的好处是可以任意选择 GDB 的版本，但由于安装过程需要编译源码，因此安装速度较慢。

> 注意，不同的 Linux 发行版，管理包的工具也不同。根据维护团体（商业公司维护和社区组织维护）的不同，可以将众多 Linux 发行版分为 2 个系列，分别为 RedHat 系列和 Debian 系列。其中 RedHat 系列代表 Linux 发行版有 RedHat、CentOS、Fedora 等，使用 yum 作为包管理器；Debian 系列有 Debian、Ubuntu 等，使用 apt 作为包管理器。

## 快速安装 GDB

对于 RedHat 系列的 Linux 发行版，通过在命令行窗口中执行`sudo yum -y install gdb`指令，即可实现 GDB 调试器的安装。这里以 CentOS 为例，执行该指令的过程为：

[root@bogon ~]# gdb -v
bash: gdb: command not found                                               <--当前系统中没有 GDB 
[root@bogon ~]# sudo yum -y install gdb                              <--安装 GDB
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
......   <-省略部分过程
Installed:
  gdb.x86_64 0:7.2-92.el6                                                      

Complete!
[root@bogon ~]# gdb -v
GNU gdb (GDB) Red Hat Enterprise Linux (7.2-92.el6)               <--安装成功

可以看到，GDB 安装成功。

对于 Debian 系列的 Linux 发行版，通过执行`sudo apt -y install gdb`指令，即可实现 GDB 的安装。感兴趣的读者可自行验证，这里不再过多赘述。

> 注意，使用 yum 或者 apt 更多情况需要借助网络下载所需使用的安装包，这也就意味着，如果读者所用系统没有网络环境，很有可能安装失败。

## 源码安装 GDB

和使用 yum（apt）自动安装 GDB 不同，手动安装需提前到 GDB 官网下载相应的源码包，读者可直接点击 [GDB 源码包](http://ftp.gnu.org/gnu/gdb/)进行下载。值得一提的是，每个 GDB 版本都提供有 tar.gz 和 tar.xz 这 2 种格式的压缩包，这里以 tar.gz 格式为例教大家安装 GDB。

> 注意，在安装 GDB 之前，读者必须保证当前操作系统中有可以使用的编译器，比如最常用的 GCC 编译器（应同时支持 gcc 和 g++ 指令），有关 GCC 编译器的下载和安装，读者可阅读《GCC 下载和安装》一文。另外，源码安装 GDB 需要用到 Makefile 相关的知识，读者可完全遵循以下步骤“照猫画虎”地安装 GDB。对 Makefile 感兴趣的读者，可前往《Makefile 教程》做系统了解。

本节下载的 GDB 源码包为 gdb-9.2-tar.gz，接下来以 CentOS 系统为例（也同样适用于其它 Linux 发行版），给大家演示整个安装过程：
1) 找到 gdb-9.2-tar.gz 文件，笔者将下载好的 gdb-9.2-tat.gz 放置在 /usr/local/src 目录下：

[root@bogon ~]# cd /usr/local/src
[root@bogon src]# ls
gdb-9.2.tar.gz

2) 使用 tar 命令解压该文件，执行命令如下：

[root@bogon ~]# tar -zxvf gdb-9.2.tar.gz
--省略解压过程的输出结果
[root@bogon src]# ls
gdb-9.2  gdb-9.2.tar.gz

此步骤会得到 gdb-9.2.tar.gz 相应的解压文件 gdb-9.2 。

3) 进入 gdb-9.2 目录文件，创建一个 gdb_build_9.2 目录并进入，为后续下载并放置安装 GDB 所需的依赖项做准备：

[root@bogon src]# cd gdb-9.2
[root@bogon gdb-9.2]# mkdir gdb-build-9.2
[root@bogon src]# cd gdb-build-9.2

4) 在此基础上，继续执行如下指令：

[root@bogon gdb-build-9.2]# ../configure
......    <--省略众多输出
configure: creating ./config.status
config.status: creating Makefile

5) 执行 make 指令编译整个 GDB 源码文件，此过程可能会花费很长时间，读者耐心等待即可：

[root@bogon gdb-build-9.2]# make
...... <-- 省略编译过程产生的输出结果

> 注意，如果编译过程中出现错误，极有可能是所用的 GCC 编译器版本过低导致的，可尝试升级 GCC 版本后再重新执行 make 命令。

6) 确定整个编译过程没有出错之后，执行`sudo make install`指令（其中使用 sudo 指令是为了避免操作权限不够而导致安装失败），正式开始安装 GDB 调试器：

[root@bogon gdb-build-9.2]# sudo make install
...... <-- 省略输出结果

以上过程全部成功执行，则表示 GDB 安装成功。通过再次执行 gdb -v 指令，可验证其是否被成功安装。

[root@bogon gdb-build-9.2]# gdb -v
GNU gdb (GDB) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
......  <-- 省略部分输出