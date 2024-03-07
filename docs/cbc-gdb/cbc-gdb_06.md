# GDB 调试 C/C++程序

> 原文：[`c.biancheng.net/view/8153.html`](http://c.biancheng.net/view/8153.html)

通过前面的学习，读者已经了解了什么是 GDB，以及如何下载并安装它。从本节开始，我们将正式学习使用 GDB 调试 C、C++ 程序。

如下是一段能够正常编译运行的 C 语言程序：

```

#include <stdio.h>
int main ()
{
    unsigned long long int n, sum;

    n = 1;
    sum = 0;

    while (n <= 100)
    {
        sum = sum + n;
        n = n + 1;
    }

    return 0;
}
```

> 此源码的完整存储路径为 /tmp/demo/main.c。

本节就以此程序为例，给大家演示 GDB 调试器的基本用法。

## 使用 GDB 的前期准备

通过前面的学习我们知道，GDB 的主要功能就是监控程序的执行流程。这也就意味着，只有当源程序文件编译为可执行文件并执行时，GDB 才会派上用场。

Linux 发行版中，经常使用 GCC 编译 C、C++ 程序（有关 GCC 编译器，读者可猛击《GCC 编译器》系统学习）。但需要注意的是，仅使用 gcc（或 g++）命令编译生成的可执行文件，是无法借助 GDB 进行调试的。

以 main.c 源文件为例，正常情况下，使用 GCC 编译该源代码的指令如下：

[root@bogon demo]# ls
main.c
[root@bogon demo]# gcc main.c -o main.exe
[root@bogon demo]# ls
main.c  main.exe

可以看到，这里已经生成了 main.c 对应的执行文件 main.exe，但值得一提的是，此文件不支持使用 GDB 进行调试。原因很简单，使用 GDB 调试某个可执行文件，该文件中必须包含必要的调试信息（比如各行代码所在的行号、包含程序中所有变量名称的列表（又称为符号表）等），而上面生成的 main.exe 则没有。

那么，如何生成符合 GDB 调试要求的可执行文件呢？很简单，只需要使用 gcc -g 选项编译源文件，即可生成满足 GDB 要求的可执行文件。仍以 main.c 源程序文件为例：

[root@bogon demo]# ls
main.c
[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c  main.exe

由此生成的 main.exe，即可使用 GDB 进行调试。

> 较早以前的 C 语言编译器也允许使用 -gg 选项来产生调试信息，但是现在版本的 GDB 不再支持这种格式产生的调试信息，所以不建议使用 -gg 选项。

值得一提的是，GCC 编译器支持 -O（等于同 -O1，优化生成的目标文件）和 -g 一起参与编译。GCC 编译过程对进行优化的程度可分为 5 个等级，分别为 O0~O4，O0 表示不优化（默认选项），从 O1 ~ O4 优化级别越来越高，O4 最高。

> 所谓优化，例如省略掉代码中从未使用过的变量、直接将常量表达式用结果值代替等等，这些操作会缩减目标文件所包含的代码量，提高最终生成的可执行文件的运行效率。

而相对于 -O -g 选项，对 GDB 调试器更友好的是 -Og 选项，-Og 对代码所做的优化程序介于 O0 ~ O1 之间，真正可做到“在保持快速编译和良好调试体验的同时，提供较为合理的优化级别”。

解决了如何生成满足 GDB 调试器要求的可执行文件，接下来正式学习 GDB 调试器的使用。

## 启动 GDB 调试器

在生成包含调试信息的 main.exe 可执行文件的基础上，启动 GDB 调试器的指令如下：

[root@bogon demo]# gdb main.exe
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
(gdb) 

注意，该指令在启动 GDB 的同时，会打印出一堆免责条款。通过添加 --silent（或者 -q、--quiet）选项，可将比部分信息屏蔽掉：

[root@bogon demo]#  gdb main.exe --silent
Reading symbols from main.exe...(no debugging symbols found)...done.
(gdb) 

无论使用以上哪种方式，最终都可以启动 GDB 调试器，启动成功的标志就是最终输出的 (gdb)。通过在 (gdb) 后面输入指令，即可调用 GDB 调试进行对应的调试工作。

GDB 调试器提供有大量的调试选项，可满足大部分场景中调试代码的需要。如表 1 所示，罗列了几个最常用的调试指令及各自的作用：

表 1 GDB 常用的调试指令

| 调试指令 | 作 用 |
| (gdb) break xxx (gdb) b xxx | 在源代码指定的某一行设置断点，其中 xxx 用于指定具体打断点的位置。 |
| (gdb) run (gdb) r | 执行被调试的程序，其会自动在第一个断点处暂停执行。 |
| (gdb) continue (gdb) c | 当程序在某一断点处停止运行后，使用该指令可以继续执行，直至遇到下一个断点或者程序结束。 |
| (gdb) next (gdb) n | 令程序一行代码一行代码的执行。 |
| (gdb) print xxx (gdb) p xxx | 打印指定变量的值，其中 xxx 指的就是某一变量名。 |
| (gdb) list (gdb) l | 显示源程序代码的内容，包括各行代码所在的行号。 |
| (gdb) quit (gdb) q | 终止调试。 |

> 如上所示，每一个指令既可以使用全拼，也可以使用其首字母表示。另外，表 1 中罗列的指令仅是冰山一角，GDB 还提供有大量的选项，可以通过 help 选项来查看。有关 help 选项的具体用法，读者可阅读《GDB 查看命令》一节，这里不再做具体赘述。

仍以 main.exe 可执行程序为例，接下来为读者演示表 1 中部分选项的功能和用法：

(gdb) l                      <-- 显示带行号的源代码
1 #include <stdio.h>
2 int main ()
3 {
4     unsigned long long int n, sum;
5     n = 1;
6     sum = 0;
7     while (n <= 100)
8     {
9         sum = sum + n;
10         n = n + 1;
(gdb)                      <-- 默认情况下，l 选项只显示 10 行源代码，如果查看后续代码，安装 Enter 回车即可                                                               
11     }
12     return 0;
13 }
(gdb) b 7               <-- 在第 7 行源代码处打断点
Breakpoint 1 at 0x400504: file main.c, line 7.
(gdb) r                   <-- 运行程序，遇到断点停止
Starting program: /home/mozhiyan/demo1/main.exe

Breakpoint 1, main () at main.c:7
7     while (n <= 100)
Missing separate debuginfos, use: debuginfo-install glibc-2.17-55.el7.x86_64
(gdb) p n               <-- 查看代码中变量 n 的值
$1 = 1                   <-- 当前 n 的值为 1，$1 表示该变量所在存储区的名称
(gdb) b 12             <-- 在程序第 12 行处打断点
Breakpoint 2 at 0x40051a: file main.c, line 12.
(gdb) c                  <-- 继续执行程序
Continuing.

Breakpoint 2, main () at main.c:12
12     return 0;
(gdb) p n               <-- 查看当前 n 变量的值
$2 = 101               <-- 当前 n 的值为 101
(gdb) q                  <-- 退出调试
A debugging session is active.

Inferior 1 [process 3080] will be killed.

Quit anyway? (y or n) y                 <-- 确实是否退出调试，y 为退出，n 为不退出
[root@bogon demo]#

> 后续章节会对以上指令做详细的讲解，这里简单了解即可，不必深究。