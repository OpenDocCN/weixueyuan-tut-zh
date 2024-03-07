# GDB 调试多进程程序

> 原文：[`c.biancheng.net/view/8274.html`](http://c.biancheng.net/view/8274.html)

GDB 调试器不只可以调试多线程程序，还可以调试多进程程序。

对于 C 和 C++ 程序而言，多进程的实现往往借助的是`<unistd.h>`头文件中的 fork() 函数或者 vfork() 函数。举个例子：

```

#include <stdio.h>
#include <unistd.h>

int main()
{
    pid_t pid = fork();
    if(pid == 0)
    {
        printf("this is child,pid = %d\n",getpid());
    }
    else
    {
        printf("this is parent,pid = %d\n",getpid());
    }
    return 0;
}
```

程序的存储路径为`~/demo/myfork.c`。可以看到，程序中包含 2 个进程，分别为父进程（又称主进程）和使用 fork() 函数分离出的子进程。

事实上在多数 Linux 发行版系统中，GDB 并没有对多进程程序提供友好的调试功能。无论程序中调用了多少次 fork() 函数（或者 vfork() 函数），从父进程中分离出多少个子进程，GDB 默认只调试父进程，而不调试子进程。

那么问题就出现了，如何使用 GDB 调试多进程程序中的子进程呢？

## GDB attach 命令调试进程

首先，无论父进程还是子进程，都可以借助 attach 命令启动 GDB 调试它。attach 命令用于调试正在运行的进程，要知道对于每个运行的进程，操作系统都会为其配备一个独一无二的 ID 号。在得知目标子进程 ID 号的前提下，就可以借助 attach 命令来启动 GDB 对其进行调试。

这里还需要解决一个问题，很多场景中子进程的执行时间都是一瞬而逝的，这意味着，我们可能还未查到它的进程 ID 号，该进程就已经执行完了，何谈借助 attach 命令对其调试呢？对于 C、C++ 多进程程序，解决该问题最简单直接的方法是，在目标进程所执行代码的开头位置，添加一段延时执行的代码。

例如，将上面程序中`if(pid==0)`判断语句整体做如下修改：

```

if(pid == 0)
{
    int num =10;
    while(num==10){
        sleep(10);
    }
    printf("this is child,pid = %d\n",getpid());
}
```

可以看到，通过添加第 3~6 行代码，该进程执行时会直接进入死循环。这样做的好处有 2 个，其一是帮助 attach 命令成功捕捉到要调试的进程；其二是使用 GDB 调试该进程时，进程中真正的代码部分尚未得到执行，使得我们可以从头开始对进程中的代码进行调试。

> 有读者可能会问，进程都已经进行死循环了，后续代码还可以进行调试吗？当然可以，以上面示例中给出的死循环，我们只需用 print 命令临时修改 num 变量的值，即可使程序跳出循环，从而执行后续代码。

就以调试修改后的 myfork.c 程序（已将其编译为 myfork.exe 可执行文件）为例：

[root@bogon demo]# gdb myfork.exe -q
Reading symbols from ~/demo/myfork.exe...done.
(gdb) r
Starting program: ~/demo/myfork.exe
Detaching after fork from child process 5316.  <-- 子进程的 ID 号为 5316
this is parent,pid = 5313               <-- 父进程执行完毕

Program exited normally.
(gdb) attach 5316                          <-- 跳转调试 ID 号为 5316 的子进程
......
(gdb) n                                           <-- 程序正在运行，所有直接使用 next 命令就可以进行单步调试
Single stepping until exit from function __nanosleep_nocancel,
which has no line number information.
0x00000037ee2acb50 in sleep () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function sleep,
which has no line number information.
main () at myfork.c:10
10  while(num==10){
(gdb) p num=1
$1 = 1
(gdb) n                                           <-- 跳出循环
13         printf("this is child,pid = %d\n",getpid());
(gdb) c
Continuing.
this is child,pid = 5316

Program exited normally.
(gdb) 

> 对于子进程 ID 号的获取，除了依靠 GDB 调试器打印出的信息，也可以使用 pidof 命令手动获取。有关 pidof 命令获取进程 ID 好的具体用法，我已经在《调用 GDB 的几种方式》一节中做了详细的讲解，这里不再重复赘述。

## GDB 显式指定要调试的进程

前面提到，GDB 调试多进程程序时默认只调试父进程。对于内核版本为 2.5.46 甚至更高的 Linux 发行版系统来说，可以通过修改 follow-fork-mode 或者 detach-on-fork 选项的值来调整这一默认设置。

#### GDB follow-fork-mode 选项

确切地说，对于使用 fork() 或者 vfork() 函数构建的多进程程序，借助 follow-fork-mode 选项可以设定 GDB 调试父进程还是子进程。该选项的使用语法格式为：

(gdb) set follow-fork-mode mode

参数 mode 的可选值有 2 个：

*   parent：选项的默认值，表示 GDB 调试器默认只调试父进程；
*   child：和 parent 完全相反，它使的 GDB 只调试子进程。且当程序中包含多个子进程时，我们可以逐一对它们进行调试。

举个例子：

(gdb) show follow-fork-mode
Debugger response to a program call of fork or vfork is "parent".
(gdb) set follow-fork-mode child                        <-- 调试子进程
(gdb) r 
Starting program: ~/demo/myfork.exe
[New process 5376]
this is parent,pid = 5375                  <-- 父进程执行完成

Program received signal SIGTSTP, Stopped (user).
[Switching to process 5376]             <-- 自动进入子进程
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function __nanosleep_nocancel,
which has no line number information.
0x00000037ee2acb50 in sleep () from /lib64/libc.so.6
(gdb) n
Single stepping until exit from function sleep,
which has no line number information.
main () at myfork.c:10
10  while(num==10){
(gdb) p num=1
$2 = 1
(gdb) c
Continuing.
this is child,pid = 5376

通过执行如下命令，我们可以轻松了解到当前调试环境中 follow-fork-mode 选项的值：

(gdb) show follow-fork-mode
Debugger response to a program call of fork or vfork is "child".

#### GDB detach-on-fork 选项

注意，借助 follow-fork-mode 选项，我们只能选择调试子进程还是父进程，且一经选定，调试过程中将无法改变。如果既想调试父进程，又想随时切换并调试某个子进程，就需要借助 detach-on-fork 选项。

detach-on-fork 选项的语法格式如下：

(gdb) set detach-on-fork mode

其中，mode 参数的可选值有 2 个：

*   on：默认值，表明 GDB 只调试一个进程，可以是父进程，或者某个子进程；
*   off：程序中出现的每个进程都会被 GDB 记录，我们可以随时切换到任意一个进程进行调试。

和 detach-on-fork 搭配使用的，还有如表 1 所示的几个命令。

表 1 GDB 多进程调试常用命令

| 命令语法格式 | 功 能 |
| (gdb)show detach-on-fork | 查看当前调试环境中 detach-on-fork 选项的值。 |
| (gdb) info inferiors | 查看当前调试环境中有多少个进程。其中，进程 id 号前带有 * 号的为当前正在调试的进程。 |
| (gdb) inferiors id | 切换到指定 ID 编号的进程对其进行调试。 |
| (gdb) detach inferior id | 断开 GDB 与指定 id 编号进程之间的联系，使该进程可以独立运行。不过，该进程仍存在 info inferiors 打印的列表中，其 Describution 列为 <null>，并且借助 run 仍可以重新启用。 |
| (gdb) kill inferior id | 断开 GDB 与指定 id 编号进程之间的联系，并中断该进程的执行。不过，该进程仍存在 info inferiors 打印的列表中，其 Describution 列为 <null>，并且借助 run 仍可以重新启用。 |
| remove-inferior id | 彻底删除指令 id 编号的进程（从 info inferiors 打印的列表中消除），不过在执行此操作之前，需先使用 detach inferior id 或者 kill inferior id 命令将该进程与 GDB 分离，同时确认其不是当前进程。 |

> 除表 1 罗列的这几个命令，GDB 调试其提供有其它的一些命令，由于不常用，这里不再罗列，读者可前往 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Inferiors-Connections-and-Programs.html#Inferiors-Connections-and-Programs)自行查看。

这里仍以调试 myfork.c 程序为例，不过为了让读者清楚地感受 detach-on-fork 选项的功能，这里需要对 else 语句块的代码进行如下修改：

```

else
{
    int mnum=5;
    while(mnum==5){
        sleep(1);
    }
    printf("this is parent,pid = %d\n",getpid());
}
```

> 也就是说，myfork.c 程序中，父进程和子进程中各拥有一个死循环。

在此基础上，进行如下调试： 

(gdb) set detach-on-fork off             <-- 令 GDB 可调试多个进程
(gdb) b 6
Breakpoint 1 at 0x11b5: file myfork.c, line 6.
(gdb) r
Starting program: ~/demo/myfork.exe

Breakpoint 1, main () at myfork.c:6
6     pid_t pid = fork();
(gdb) n
[New inferior 2 (process 5163)]           <-- 新增一个子进程，ID 号为 5163
Reading symbols from ~/demo/myfork.exe...
Reading symbols from /usr/lib/debug/lib/x86_64-linux-gnu/libc-2.31.so...
7     if(pid == 0)
(gdb) n                                                <-- 由于 GDB 默认调试父进程，因此进入 else 语句
17     int mnum=5;
(gdb) info inferiors     <-- 查看当前调试环境中的进程数，当前有 2 个进程，1 号进程为当前正在调试的进程
  Num  Description       Executable       
* 1    process 5159      ~/demo/myfork.exe
  2    process 5163       ~/demo/myfork.exe
(gdb) inferior 2                                   <-- 进入 id 号为 2 的子进程
[Switching to inferior 2 [process 5163] (~/demo/myfork.exe)]
[Switching to thread 2.1 (process 5163)]
(gdb) n
53 in ../sysdeps/unix/sysv/linux/arch-fork.h
(gdb) n
__libc_fork () at ../sysdeps/nptl/fork.c:78
78 ../sysdeps/nptl/fork.c: No such file or directory.
(gdb) n
......                                                       <-- 执行多个 next 命令
(gdb) n
main () at myfork.c:7                           <-- 正式单步调试子进程  
7     if(pid == 0)
(gdb) n
9         int num =10;
(gdb)

可以看到，通过设置 detach-on-fork 选项值为 off，再配合使用 info inferiors 等命令，即可随意切换到当前环境中的各个进程，并对它们进行调试。

> 感兴趣的读者，可自行以默认模式（即 detach-on-fork 值为 on）调试该程序，对比它们之间的区别。