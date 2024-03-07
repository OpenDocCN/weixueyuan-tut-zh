# GDB watch 命令：监控变量值的变化

> 原文：[`c.biancheng.net/view/8191.html`](http://c.biancheng.net/view/8191.html)

《GDB break 命令》一节，给大家介绍了使用 break 命令在程序某一行的位置打断点。但还有一些场景，我们需要监控某个变量或者表达式的值，通过值的变化情况判断程序的执行过程是否存在异常或者 Bug。这种情况下，break 命令显然不再适用，推荐大家使用 watch 命令。

要知道，GDB 调试器支持在程序中打 3 种断点，分别为普通断点、观察断点和捕捉断点。其中 break 命令打的就是普通断点，而 watch 命令打的为观察断点，关于捕捉断点，后续章节会做详细讲解。

使用 GDB 调试程序的过程中，借助观察断点可以监控程序中某个变量或者表达式的值，只要发生改变，程序就会停止执行。相比普通断点，观察断点不需要我们预测变量（表达式）值发生改变的具体位置。

> 所谓表达式，就是包含多个变量的式子，比如 a+b 就是一个表达式，其中 a、b 为变量。

对于监控 C、C++ 程序中某变量或表达式的值是否发生改变，watch 命令的语法非常简单，如下所示：

(gdb) watch cond

其中，conde 指的就是要监控的变量或表达式。

和 watch 命令功能相似的，还有 rwatch 和 awatch 命令。其中：

*   rwatch 命令：只要程序中出现读取目标变量（表达式）的值的操作，程序就会停止运行；
*   awatch 命令：只要程序中出现读取目标变量（表达式）的值或者改变值的操作，程序就会停止运行。

> 强调一下，watch 命令的功能是：只有当被监控变量（表达式）的值发生改变，程序才会停止运行。

举个例子：

(gdb) l           <--列出要调试的程序源码
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4     int num = 1;
5     while(num<=100)
6     {
7         num *= 2;
8     }
9     printf("%d",num);
10     return 0;
(gdb)
11 }
(gdb) b 4       <-- 使用 break 命令打断点
Breakpoint 1 at 0x115c: file main.c, line 4.
(gdb) r           <-- 执行程序
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, main (argc=1, argv=0x7fffffffe088) at main.c:4
4     int num = 1;
(gdb) watch num   <-- 监控程序中 num 变量的值
Hardware watchpoint 2: num
(gdb) c            <-- 继续执行，当 num 值发生改变时，程序才停止执行
Continuing.

Hardware watchpoint 2: num

Old value = 0
New value = 2
main (argc=1, argv=0x7fffffffe088) at main.c:5
5     while(num<=100)
(gdb) c           <-- num 值发生了改变，继续执行程序
Continuing.

Hardware watchpoint 2: num

Old value = 2
New value = 4
main (argc=1, argv=0x7fffffffe088) at main.c:5
5     while(num<=100)
(gdb)

> 有关代码中蓝色部分的含义，本文后续会做详细解释。

可以看到在程序运行过程中，通过借助 watch 命令监控 num 的值，后续只要 num 的值发生改变，程序都会停止。感兴趣的读者，可自行尝试使用 awatch 和 rwatch 命令，这里不再给出具体的示例。

如果我们想查看当前建立的观察点的数量，借助如下指令即可：

(gdb) info watchpoints

值得一提的是，对于使用 watch（rwatch、awatch）命令监控 C、C++ 程序中变量或者表达式的值，有以下几点需要注意：

*   当监控的变量（表达式）为局部变量（表达式）时，一旦局部变量（表达式）失效，则监控操作也随即失效；
*   如果监控的是一个指针变量（例如 *p），则 watch *p 和 watch p 是有区别的，前者监控的是 p 所指数据的变化情况，而后者监控的是 p 指针本身有没有改变指向；
*   这 3 个监控命令还可以用于监控数组中元素值的变化情况，例如对于 a[10] 这个数组，watch a 表示只要 a 数组中存储的数据发生改变，程序就会停止执行。

> 如果读者只想学习如何使用 watch 命令，则读者这里即可。反之，如果想了解 watch 命令底层是如何实现的，可以继续往下阅读。

## watch 命令的实现原理

watch 命令实现监控机制的方式有 2 种，一种是为目标变量（表达式）设置硬件观察点，另一种是为目标变量（表达式）设置软件观察点。

所谓软件观点（software watchpoint），即用 watch 命令监控目标变量（表达式）后，GDB 调试器会以单步执行的方式运行程序，并且每行代码执行完毕后，都会检测该目标变量（表达式）的值是否发生改变，如果改变则程序执行停止。

可想而知，这种“实时”的判别方式，一定程度上会影响程序的执行效率。但从另一个角度看，调试程序的目的并非是为了获得运行结果，而是查找导致程序异常或 Bug 的代码，因此即便软件观察点会影响执行效率，一定程度上也是可以接受的。

所谓硬件观察点（Hardware watchpoint），和前者最大的不同是，它在实现监控机制的同时不影响程序的执行效率。简单的理解，系统会为 GDB 调试器提供少量的寄存器（例如 32 位的 Intel x86 处理器提供有 4 个调试寄存器），每个寄存器都可以作为一个观察点协助 GDB 完成监控任务。

需要注意的是，基于寄存器个数的限制，如果调试环境中设立的硬件观察点太多，则有些可能会失去作用，这种情况下，GDB 调试器会发出如下警告：

Hardware watchpoint num: Could not insert watchpoint

解决方案也很简单，就是删除或者禁用一部分硬件观察点。

除此之外，受到寄存器数量的限制，可能会出现：无法使用硬件观察点监控数据类型占用字节数较多的变量（表达式）。比如说，某些操作系统中，GDB 调试器最多只能监控 4 个字节长度的数据，这意味着 C、C++ 中 double 类型的数据是无法使用硬件观察点监测的。这种情况下，可以考虑将其换成占用字符串少的 float 类型。

目前，大多数 PowerPC 或者基于 x86 的操作系统，都支持采用硬件观点。并且 GDB 调试器在建立观察断点时，会优先尝试建立硬件观察点，只有当前环境不支持硬件观察点时，才会建立软件观察点。借助如下指令，即可强制 GDB 调试器只建立软件观察点：

set can-use-hw-watchpoints 0

> 注意，在执行此命令之前建立的硬件观察点，不会受此命令的影响。

注意，awatch 和 rwatch 命令只能设置硬件观察点，如果系统不支持或者借助如上命令禁用，则 GDB 调试器会打印如下信息：

Expression cannot be implemented with read/access watchpoint.