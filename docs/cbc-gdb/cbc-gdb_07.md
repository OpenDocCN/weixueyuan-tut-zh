# 调用 GDB 调试器的几种方式

> 原文：[`c.biancheng.net/view/8166.html`](http://c.biancheng.net/view/8166.html)

《GDB 调试 C/C++程序》一节演示了用 GDB 调试 C（或者 C++）程序的整个过程，其中对 main.exe 文件启动 GDB 调试，执行的指令为：

[root@bogon demo]# gdb main.exe
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
(gdb) 

要知道，这仅是调用 GDB 调试器最常用的一种方式，GDB 调试器还有其它的启动方式。并且，为了满足不同场景的需要，启动 GDB 调试器时还可以使用一些参数选项，从而控制它启动哪些服务或者不启动哪些服务。

## 调用 GDB 的方式

总的来说，调用 GDB 调试器的方法有 4 种。
1) 直接使用 gdb 指令启动 GDB 调试器：

[root@bogon demo]# gdb
ubuntu64@ubuntu64-virtual-machine:~/demo$ gdb
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......  <-- 省略部分输出信息
Type "apropos word" to search for commands related to "word".
(gdb)

此方式启动的 GDB 调试器，由于事先未指定要调试的具体程序，因此需启动后借助 file 或者 exec-file 命令指定（后续章节会做详细讲解）。

#### 2) 调试尚未执行的程序

对于具备调试信息（使用 -g 选项编译而成）的可执行文件，调用 GDB 调试器的指令格式为：

gdb program

其中，program 为可执行文件的文件名，例如上节创建好的 main.exe。

#### 3) 调试正在执行的程序

在某些情况下，我们可能想调试一个当前已经启动的程序，但又不想重启该程序，就可以借助 GDB 调试器实现。

也就是说，GDB 可以调试正在运行的 C、C++ 程序。要知道，每个 C 或者 C++ 程序执行时，操作系统会使用 1 个（甚至多个）进程来运行它，并且为了方便管理当前系统中运行的诸多进程，每个进程都配有唯一的进程号（PID）。

如果需要使用 GDB 调试正在运行的 C、C++ 程序，需要事先找到该程序运行所对应的进程号。查找方式很简单，执行如下命令即可：

pidof 文件名

比如，我们将上节创建的 main.c 源文件修改为：

```

#include <stdio.h>
int main()
{
    int num = 1;
    while(1)
    {
        num++;
    }
    return 0;
}
```

执行 gcc main.c -o main.exe -g 编译指令，获得该源程序对应的具备调试信息的 main.exe 可执行文件，并在此基础上执行：

[root@bogon demo]# ./main.exe
        <--程序一直运行

显然，程序中存在死循环（5~8 行），它会一直执行。此时，借助 pidof 指令即可获取它对应的进程号：

[root@bogon demo]# pidof main.exe
1830

可以看到，当前正在执行的 main.exe 对应的进程号为 1830。在此基础上，可以调用 GDB 对该程序进行调试，调用指令有以下 3 种形式：

1) gdb attach PID
2) gdb 文件名 PID
3) gdb -p PID

其中，PID 指的就是要调取的程序对应的进程号。

以调试进程号为 1830 的程序为例，执行如下指令：

[root@bogon demo]# gdb -p 1830
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......  <-- 省略部分输出信息
0x00005645319c613c in main () at main.c:6
6         num++;

注意，当 GDB 调试器成功连接到指定进程上时，程序执行会暂停。如上所示，程序暂停至第 6 行代码 num++ 的位置，此时可以通过断点调试、逐步运行等方式监控程序的执行过程。例如：

(gdb) l     <-- 查看源码以及各行行号
1 #include<stdio.h>
2 int main()
3 {
4     int num = 1;
5     while(1){
6         num++;
7     }
8     return 0;
9 }
(gdb) b 6      <--在程序第 6 行代码处打断点
Breakpoint 1 at 0x5645319c6138: file main.c, line 6.
(gdb) c         <--令程序进行执行，其会在下一个断点处停止
Continuing.

Breakpoint 1, main () at main.c:6
6         num++;
(gdb) p num         <-- 查看当前 num 的值
$2 = 47100335

注意，当调试完成后，如果想令当前程序进行执行，消除调试操作对它的影响，需手动将 GDB 调试器与程序分离，分离过程分为 2 步：

1.  执行 detach 指令，使 GDB 调试器和程序分离；
2.  执行 quit（或 q）指令，退出 GDB 调试。

#### 4) 调试执行异常崩溃的程序

除了以上 3 种情况外，C 或者 C++ 程序运行过程中常常会因为各种异常或者 Bug 而崩溃，比如内存访问越界（例如数组下标越界、输出字符串时该字符串没有 \0 结束符等）、非法使用空指针等，此时就需要调试程序。

值得一提的是，在 Linux 操作系统中，当程序执行发生异常崩溃时，系统可以将发生崩溃时的内存数据、调用堆栈情况等信息自动记录下载，并存储到一个文件中，该文件通常称为 core 文件，Linux 系统所具备的这种功能又称为核心转储（core dump）。幸运的是，GDB 对 core 文件的分析和调试提供有非常强大的功能支持，当程序发生异常崩溃时，通过 GDB 调试产生的 core 文件，往往可以更快速的解决问题。

默认情况下，Linux 系统是不开启 core dump 这一功能的，读者可以借助执行`ulimit -c`指令来查看当前系统是否开启此功能：

[root@bogon demo]# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
......

其中，如果 core file size（core 文件大小）对应的值为 0，表示当前系统未开启 core dump 功能。这种情况下，可以通过执行如下指令改变 core 文件的大小：

[root@bogon demo]# ulimit -c unlimited
[root@bogon demo]# ulimit -a
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
......

> 其中，unlimited 表示不限制 core 文件的大小。

由此，当程序执行发生异常崩溃时，系统就可以自动生成相应的 core 文件。

举个例子，修改 main.c 源程序文件中的代码为：

```

#include <stdio.h>
int main()
{
    char *p = NULL;
    *p = 123;
    return 0;
}
```

重新编译，即执行如下指令：

[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ./main.exe
Segmentation fault (core dumped)   <--发生段错误，并生成了 core 文件
[root@bogon demo]# ls
core  main.c  main.exe

> 段错误又称为访问权限冲突，指的是当前程序访问了不可访问的存储空间，比如访问的不存在的空间，又或者是受系统保护的内存空间。

观察此程序不难发现，由于 p 指针初始化为 NULL，即不指向任何存储空间，但后续却执行`*p=123`操作，显然是不可行的。因此，该程序执行时会发生崩溃，Linux 系统会记录必要的崩溃信息，并存储到 core 文件中。

> 默认情况下，core 文件的生成位置同该程序所在的目录相同。当然我们也可以指定 core 文件的生成的位置，感兴趣的读者可自行研究，这里不再介绍。

对于 core 文件的调试，其调用 GDB 调试器的指令为：

[root@bogon demo]# gdb main.exe core
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......
Reading symbols from main.exe...
[New LWP 4296]

warning: Unexpected size of section `.reg-xstate/4296' in core file.
Core was generated by `./main.exe'.
Program terminated with signal SIGSEGV, Segmentation fault.

warning: Unexpected size of section `.reg-xstate/4296' in core file.
#0  0x00005583b933013d in main () at main.c:5
5     *p = 123;

可以看到，程序发生崩溃的位置是在 main.c 中的第 5 行。甚至于，对于 core 文件中记录的崩溃信息，可以使用 where、print、bt 等指令查看，有关这些指令的功能和用法，由于并非本节重点，这里不再具体赘述，后续章节会做详细讲解。

## GDB 调试器启动可用参数

表 1 罗列了一些在启动 GDB 调试器时常用的指令参数，以及它们各自的功能。

表 1 启动 GDB 调试器可用参数

| 参 数 | 功 能 |
| -pid number -p number | 调试进程 ID 为 number 的程序。 |
| -symbols file -s file | 仅从指定 file 文件中读取符号表。 |
| -q -quiet
-silent | 取消启动 GDB 调试器时打印的介绍信息和版权信息 |
| -cd directory | 以 directory 作为启动 GDB 调试器的工作目录，而非当前所在目录。 |
| --args 参数 1 参数 2... | 向可执行文件传递执行所需要的参数。 |

其中有些参数，我们已经在前面的学习给大家做了具体的演示，这里不再重复赘述，读者可自行尝试使用。除此之外，启动 GDB 调试器时还有其它参数指令可以使用，感兴趣的读者可查阅 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Invoking-GDB.html#Invoking-GDB)做系统了解。有关表 1 以及 GDB 调试器支持的其它指令，后续章节用到时会做详细讲解。