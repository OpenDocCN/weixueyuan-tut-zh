# GDB break（b）：设置断点

> 原文：[`c.biancheng.net/view/8189.html`](http://c.biancheng.net/view/8189.html)

默认情况下，程序不会进入调试模式，代码会瞬间从开头执行到末尾。要想观察程序运行的内部细节（例如某变量值的变化情况），可以借助 GDB 调试器在程序中的某个地方设置断点，这样当程序执行到这个地方时就会停下来。

所谓断点（BreakPoint），读者可以理解为障碍物，人遇到障碍物不能行走，程序遇到断点就暂停执行。

在 GDB 调试器中对  C、C++ 程序打断点，最常用的就是 break 命令，有些场景中还会用到 tbreak 或者 rbreak 命令，本节将对这 3 个命令的功能和用法做详细的讲解。

为了让大家更好地了解给程序打断点的作用，这里以一段完整的 C 语言程序为例：

```

#include<stdio.h>
int main(int argc,char* argv[])
{
    int num = 1;
    while(num<100)
    {
        num *= 2;
    }
    printf("num=%d",num);
    return 0;
}
```

程序存储在`/tmp/demo/main.c`文件中，并已经生成了具备调试信息的 main.exe 可执行文件：

[root@bogon demo]# ls
main.c  main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...
(gdb)

## GDB break 命令

break 命令（可以用 b 代替）常用的语法格式有以下 2 种。

1、(gdb) break location     // b location
2、(gdb) break ... if cond   // b .. if cond

1) 第一种格式中，location 用于指定打断点的具体位置，其表示方式有多种，如表 1 所示。

表 1 location 参数的表示方式

| location 的值 | 含 义 |
| linenum | linenum 是一个整数，表示要打断点处代码的行号。要知道，程序中各行代码都有对应的行号，可通过执行 l（小写的 L）命令看到。 |
| filename:linenum | filename 表示源程序文件名；linenum 为整数，表示具体行数。整体的意思是在指令文件 filename 中的第 linenum 行打断点。 |
| + offset - offset | offset 为整数（假设值为 2），+offset 表示以当前程序暂停位置（例如第 4 行）为准，向后数 offset 行处（第 6 行）打断点；-offset 表示以当前程序暂停位置为准，向前数 offset 行处（第 2 行）打断点。 |
| function | function 表示程序中包含的函数的函数名，即 break 命令会在该函数内部的开头位置打断点，程序会执行到该函数第一行代码处暂停。 |
| filename:function | filename 表示远程文件名；function 表示程序中函数的函数名。整体的意思是在指定文件 filename 中 function 函数的开头位置打断点。 |

2) 第二种格式中，... 可以是表 1 中所有参数的值，用于指定打断点的具体位置；cond 为某个表达式。整体的含义为：每次程序执行到 ... 位置时都计算 cond 的值，如果为 True，则程序在该位置暂停；反之，程序继续执行。

如下演示了以上 2 种打断点方式的具体用法：

(gdb) l
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4     int num = 1;
5     while(num<100)
6     {
7         num *= 2;
8     }
9     printf("num=%d",num);
10   return 0;
(gdb)
11 }
(gdb) b 4          <-- 程序第 4 行打断点
Breakpoint 1 at 0x1138: file main.c, line 4.
(gdb) r              <-- 运行程序，至第 4 行暂停
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, main (argc=1, argv=0x7fffffffe078) at main.c:4
4     int num = 1;
(gdb) b +1        <-- 在第 4 行的基础上，在第 5 行代码处打断点
Breakpoint 2 at 0x55555555513f: file main.c, line 5.
(gdb) c             <-- 继续执行程序，至第 5 行暂停
Continuing.

Breakpoint 2, main (argc=1, argv=0x7fffffffe078) at main.c:5
5     while(num<100)
(gdb) b 7 if num>10     <-- 如果 num>10 在第 7 行打断点
Breakpoint 3 at 0x555555555141: file main.c, line 7.
(gdb) c               <-- 继续执行
Continuing.

Breakpoint 3, main (argc=1, argv=0x7fffffffe078) at main.c:7
7         num *= 2;       <-- 程序在第 7 行暂停
(gdb) p num      <-- p 命令查看 num 当前的值
$1 = 16             <-- num=16

> 有关 c 和 p 命令，后续章节会做详细讲解，这里会用即可，不必深究。

## GDB tbreak 命令

tbreak 命令可以看到是 break 命令的另一个版本，tbreak 和 break 命令的用法和功能都非常相似，唯一的不同在于，使用 tbreak 命令打的断点仅会作用 1 次，即使程序暂停之后，该断点就会自动消失。

tbreak 命令的使用格式和 break 完全相同，有以下 2 种：

1、(gdb) tbreak location
2、(gdb) tbreak ... if cond

其中，location、... 和 cond 的含义都和 break 命令中的参数含义相同，即表 1 也同样适用于 tbreak 命令。

仍以 main.exe 为例，如下演示了 tbreak 命令的用法：

(gdb) tbreak 7 if num>10
Temporary breakpoint 1 at 0x1165: file main.c, line 7.
(gdb) r
Starting program: /home/ubuntu64/demo/main.exe

Temporary breakpoint 1, main (argc=1, argv=0x7fffffffe088) at main.c:7
7         num *= 2;
(gdb) p num
$1 = 16
(gdb) c       <-- 继续执行程序，则原使用 tbreak 在第 7 行打的断点将不再起作用
Continuing.
num=128[Inferior 1 (process 6534) exited normally]
(gdb)

可以看到，自`num=16`开始，后续循环过程中 num 的值始终大于 10，则`num>10`表达式的值永远为 True，理应在第 7 行暂停多次。但由于打断点采用的是 tbreak 命令，因此断点的作用只起 1 次。

## GDB rbreak 命令

和 break 和 tbreak 命令不同，rbreak 命令的作用对象是 C、C++ 程序中的函数，它会在指定函数的开头位置打断点。

tbreak 命令的使用语法格式为：

(gdb) tbreak regex

其中 regex 为一个正则表达式，程序中函数的函数名只要满足 regex 条件，tbreak 命令就会其内部的开头位置打断点。值得一提的是，tbreak 命令打的断点和 break 命令打断点的效果是一样的，会一直存在，不会自动消失。

这里我们对 main.c 源文件的程序做如下修改：

(gdb) l      <-- 显示源码
1 #include<stdio.h>
2 void rb_one(){
3    printf("rb_one\n");
4 }
5 void rb_second(){
6   printf("rb_second");
7 }
8 int main(int argc,char* argv[])
9 {
10     rb_one();
(gdb)
11     rb_second();
12     return 0;
13 }
(gdb) rbreak rb_*       <--匹配所有以 rb_ 开头的函数
Breakpoint 1 at 0x1169: file main.c, line 2.
void rb_one();
Breakpoint 2 at 0x1180: file main.c, line 5.
void rb_second();
(gdb) r
Starting program: /home/ubuntu64/demo/main.exe

Breakpoint 1, rb_one () at main.c:2
2 void rb_one(){
(gdb) c
Continuing.
rb_one

Breakpoint 2, rb_second () at main.c:5
5 void rb_second(){
(gdb) c
Continuing.
rb_second[Inferior 1 (process 7882) exited normally]
(gdb)

可以看到，通过执行`rbreak rb_*`指令，找到了程序中所有以 tb_* 开头的函数，并在这些函数内部的开头位置打上了断点（如上所示，分别为第 2 行和第 5 行）。

## 总结

在 GDB 调试器中，为程序的适当位置打断点，是最常用的调试程序的方法。不过，本节仅介绍了如何使用 break（tbreak、rbreak）在程序中打断点，实际场景中还可以使用 catch 或者 watch 中断程序的运行，有关它们的功能和用法，会在后续章节中给大家做详细讲解。