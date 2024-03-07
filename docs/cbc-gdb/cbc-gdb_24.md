# GDB frame 和 backtrace 命令：查看栈信息

> 原文：[`c.biancheng.net/view/8282.html`](http://c.biancheng.net/view/8282.html)

当程序因某种异常停止运行时，我们要做的就是找到程序停止的具体位置，分析导致程序停止的原因。

对于 C、C++ 程序而言，异常往往出现在某个函数体内，例如 main() 主函数、调用的系统库函数或者自定义的函数等。要知道，程序中每个被调用的函数在执行时，都会生成一些必要的信息，包括：

*   函数调用发生在程序中的具体位置；
*   调用函数时的参数；
*   函数体内部各局部变量的值等等。

这些信息会集中存储在一块称为“栈帧”的内存空间中。也就是说，程序执行时调用了多少个函数，就会相应产生多少个栈帧，其中每个栈帧自函数调用时生成，函数调用结束后自动销毁。

注意，这些栈帧所在的位置也不是随意的，它们集中位于一个大的内存区域里，我们通常将其称为栈区或者栈。

> 每个 C、C++ 程序在执行时，都会占用一整块内存空间。不仅如此，整块内存空间还会进行更细致的划分，例如栈区、堆区、全局数据区、常量区等，以便于将程序中不同的资源存放在不同的的内存区域中。有关各个区域的具体作用，读者可阅读《Linux 下 C 语言程序的内存布局》做详细了解。

这也就意味着，当程序因某种异常暂停执行时，如果其发生在某个函数内部，我们可以尝试借助该函数对应栈帧中记录的信息，找到程序发生异常的原因。

庆幸的是，GDB 调试器为了方便用户在调试程序时查看某个栈帧中记录的信息，提供了 frame 和 backtrace 命令。接下来，我将给读者详细讲解一下这 2 个命令的功能和用法。

## GDB frame 命令

通过阅读上文我们知道，任何一个被调用的函数，执行时都会生成一个存储必要信息的栈帧。对于 C、C++ 程序而言，其至少也要包含一个函数，即 main() 主函数，这意味着程序执行时至少会生成一个栈帧。

> main() 主函数对应的栈帧，又称为初始帧或者最外层的帧。

除此之外，每当程序中多调用一个函数，执行过程中就会生成一个新的栈帧。更甚者，如果该函数是一个递归函数，则会生成多个栈帧。

在程序内部，各个栈帧用地址作为它们的标识符，注意这里的地址并不一定为栈帧的起始地址。我们知道，每个栈帧往往是由连续的多个字节构成，每个字节都有自己的地址，不同操作系统为栈帧选定地址标识符的规则不同，它们会选择其中一个字节的地址作为栈帧的标识符。

然而，GDB 调试器并没有套用地址标识符的方式来管理栈帧。对于当前调试环境中存在的栈帧，GDB 调试器会按照既定规则对它们进行编号：当前正被调用函数对应的栈帧的编号为 0，调用它的函数对应栈帧的编号为 1，以此类推。

frame 命令的常用形式有 2 个：
1) 根据栈帧编号或者栈帧地址，选定要查看的栈帧，语法格式如下：

(gdb) frame spec

该命令可以将 spec 参数指定的栈帧选定为当前栈帧。spec 参数的值，常用的指定方法有 3 种：

1.  通过栈帧的编号指定。0 为当前被调用函数对应的栈帧号，最大编号的栈帧对应的函数通常就是 main() 主函数；
2.  借助栈帧的地址指定。栈帧地址可以通过 info frame 命令（后续会讲）打印出的信息中看到；
3.  通过函数的函数名指定。注意，如果是类似递归函数，其对应多个栈帧的话，通过此方法指定的是编号最小的那个栈帧。

除此之外，对于选定一个栈帧作为当前栈帧，GDB 调试器还提供有 up 和 down 两个命令。其中，up 命令的语法格式为：

(gdb) up n

其中 n 为整数，默认值为 1。该命令表示在当前栈帧编号（假设为 m）的基础上，选定 m+n 为编号的栈帧作为新的当前栈帧。

相对地，down 命令的语法格式为：

(gdb) down n

其中 n 为整数，默认值为 1。该命令表示在当前栈帧编号（假设为 m）的基础上，选定 m-n 为编号的栈帧作为新的当前栈帧。

2) 借助如下命令，我们可以查看当前栈帧中存储的信息：

(gdb) info frame

该命令会依次打印出当前栈帧的如下信息：

*   当前栈帧的编号，以及栈帧的地址；
*   当前栈帧对应函数的存储地址，以及该函数被调用时的代码存储的地址
*   当前函数的调用者，对应的栈帧的地址；
*   编写此栈帧所用的编程语言；
*   函数参数的存储地址以及值；
*   函数中局部变量的存储地址；
*   栈帧中存储的寄存器变量，例如指令寄存器（64 位环境中用 rip 表示，32 为环境中用 eip 表示）、堆栈基指针寄存器（64 位环境用 rbp 表示，32 位环境用 ebp 表示）等。

除此之外，还可以使用`info args`命令查看当前函数各个参数的值；使用`info locals`命令查看当前函数中各局部变量的值。

## GDB backtrace 命令

backtrace 命令用于打印当前调试环境中所有栈帧的信息，常用的语法格式如下：

(gdb) backtrace [-full] [n]

其中，用 [ ] 括起来的参数为可选项，它们的含义分别为：

*   n：一个整数值，当为正整数时，表示打印最里层的 n 个栈帧的信息；n 为负整数时，那么表示打印最外层 n 个栈帧的信息；
*   -full：打印栈帧信息的同时，打印出局部变量的值。

> 除此之外，backtrace 命令还有其它可选参数，感兴趣的读者可自行前往 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Backtrace.html#Backtrace)查看。

注意，当调试多线程程序时，该命令仅用于打印当前线程中所有栈帧的信息。如果想要打印所有线程的栈帧信息，应执行`thread apply all backtrace`命令。

基于以上对 frame 和 backtrace 命令的介绍，这里以调试如下 C 语言程序为例，给大家演示这 2 个命令的作用。

```

#include <stdio.h>
int func(int num){
    if(num==1){
        return 1;
    }else{
        return num*func(num-1);
    }
}
int main ()
{
    int n = 5;
    int result = func(n);
    printf("%d! = %d",n,result);
    return 0;
}
```

不难发现，func() 是一个递归函数。该程序存储在`~/demo/main.c`文件中，并已编译为可供 GDB 调试的 main.exe 可执行文件。在此基础上，进行如下调试：

(gdb) b 3
Breakpoint 1 at 0x4004cf: file main.c, line 3.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, func (num=5) at main.c:3
3     if(num==1){
(gdb) c
Continuing.

Breakpoint 1, func (num=4) at main.c:3
3     if(num==1){
(gdb) p num
$1 = 4
(gdb) backtrace        <-- 打印所有的栈帧信息
#0  func (num=4) at main.c:3
#1  0x00000000004004e9 in func (num=5) at main.c:6
#2  0x0000000000400508 in main () at main.c:12
(gdb) info frame       <-- 打印当前栈帧的详细信息
Stack level 0, frame at 0x7fffffffe240:            <-- 栈帧编号 0，地址 0x7fffffffe240
rip = 0x4004cf in func (main.c:3); saved rip 0x4004e9   <-- 函数的存储地址 0x4004cf，调用它的函数地址为 0x4004e9
called by frame at 0x7fffffffe260        <-- 当前栈帧的上一级栈帧（编号 1 的栈帧）的地址为 0x7fffffffe260
source language c.
Arglist at 0x7fffffffe230, args: num=4  <-- 函数参数的地址和值
Locals at 0x7fffffffe230, Previous frame's sp is 0x7fffffffe240  <--函数内部局部变量的存储地址
Saved registers:    <-- 栈帧内部存储的寄存器
  rbp at 0x7fffffffe230, rip at 0x7fffffffe238
(gdb) info args          <-- 打印当前函数参数的值
num = 4
(gdb) info locals        <-- 打印当前函数内部局部变量的信息（这里没有）
No locals.
(gdb) up   <-- 查看编号为 1 的栈帧
#1  0x00000000004004e9 in func (num=5) at main.c:6
6         return num*func(num-1);
(gdb) frame 1           <-- 当编号为 1 的栈帧作为当前栈帧
#1  0x00000000004004e9 in func (num=5) at main.c:6
6         return num*func(num-1);
(gdb) info frame      <-- 打印 1 号栈帧的详细信息
Stack level 1, frame at 0x7fffffffe260:
rip = 0x4004e9 in func (main.c:6); saved rip 0x400508
called by frame at 0x7fffffffe280, caller of frame at 0x7fffffffe240  <--上一级栈帧地址为 0x7fffffffe280，下一级栈帧地址为 0x7fffffffe240
source language c.
Arglist at 0x7fffffffe250, args: num=5
Locals at 0x7fffffffe250, Previous frame's sp is 0x7fffffffe260
Saved registers:
  rbp at 0x7fffffffe250, rip at 0x7fffffffe258
(gdb)

> 篇幅有限，这里不再做更多的命令演示，读者可自行验证其它命令格式的用法。