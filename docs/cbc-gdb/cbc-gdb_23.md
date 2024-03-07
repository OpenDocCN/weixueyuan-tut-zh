# GDB handle 命令：信号处理

> 原文：[`c.biancheng.net/view/8291.html`](http://c.biancheng.net/view/8291.html)

C、C++ 程序中，信号常常作为进程间通信的一种重要手段。举个例子：

```

#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void display(){
    printf("http://c.biancheng.net/gdb/");
}
int main ()
{
    pid_t cpid;
    pid_t ppid;
    signal(SIGINT,display);
    if((cpid=fork())==0){
        printf("in cpid\n");
        ppid = getppid();
        kill(ppid,SIGINT);
    }else{
        wait(NULL);
    }
    return 0;
}
```

上面程序中存在 2 个进程，分别为 cpid 子进程和 ppid 主进程。cpid 子进程通过 kill() 函数向 ppid 主进程发送了 SIGINT 信号，当主进程接收到此信号时，会由等待状态转而执行 display() 函数。因此程序的执行结果为：

in cpid
http://c.biancheng.net/gdb/

其中，第一行为 cpid 子进程打印输出的，第二行为 cpid 向 ppid 主进程发出 SIGINT 信号后由 ppid 主进程打印输出的。

值得一提的是，GDB 调试器可以自动捕获 C、C++ 程序中出现的信号，并根据事先约定好的方式处理它（具体如何约定，本节后续会讲）。Linux 系统中已经事先定义好了诸多中信号，我们可以通过执行如下命令查看：

```

[root@bogon demo]# kill -l
1) SIGHUP  2) SIGINT  3) SIGQUIT  4) SIGILL  5) SIGTRAP
6) SIGABRT  7) SIGBUS  8) SIGFPE  9) SIGKILL 10) SIGUSR1
11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM
16) SIGSTKFLT 17) SIGCHLD 18) SIGCONT 19) SIGSTOP 20) SIGTSTP
21) SIGTTIN 22) SIGTTOU 23) SIGURG 24) SIGXCPU 25) SIGXFSZ
.......   <-- 省略部分输出
```

其中，每个信号代表着不同的含义，以 SIGINT 信号为例，它表示程序停止执行，该信号可以通过按 `Ctrl+c`组合键发出。换句话说，对于正在执行的程序，通过按`Ctrl+c`键向程序发出 SIGINT 信号，可以使程序停止执行。

与此同时，GDB 调试器提供了`info handles`指令，用于查看 GDB 可以处理的信号种类，以及各个信号的具体处理方式。例如：

```

(gdb) info signals
Signal        Stop Print  Pass to program  Description

SIGHUP        Yes  Yes    Yes              Hangup
SIGINT        Yes  Yes    No               Interrupt
SIGQUIT       Yes  Yes    Yes              Quit
SIGILL        Yes  Yes    Yes              Illegal instruction
SIGTRAP       Yes  Yes    No               Trace/breakpoint trap
SIGABRT       Yes  Yes    Yes              Aborted
SIGEMT        Yes  Yes    Yes              Emulation trap
SIGFPE        Yes  Yes    Yes              Arithmetic exception
SIGKILL       Yes  Yes    Yes              Killed
......
```

其中各列的含义分别为：

*   Signal：各个信号的名称；
*   Stop：当信号发生时，是否终止程序执行。Yes 表示终止，No 表示当信号发生时程序认可继续执行；
*   Print：当信号发生时，是否要求 GDB 打印出一条提示信息。Yes 表示打印，No 表示不打印；
*   Pass：当信号发生时，该信号是否对程序可见。Yes 表示程序可以捕捉到该信息，No 表示程序不会捕捉到该信息；
*   Description：对信号所表示含义的简单描述。

> 有关以上各个信号所表示的具体含义，由于不是本节重点，这里不做详细赘述。感兴趣的读者可以自行查找资料。

显然，对于现有的所有信号，GDB 调试器会根据 Stop、Print 以及 Pass 列的值进行相应的处理。当然，GDB 调试器提供了 handle 命令，由此我们就可以通过修改目标信号 Stop、Print、Pass 列的值，调试 GDB 调试器对目标信号的处理方式。

## GDB handle 命令

handle 命令的语法格式如下：

(gdb) handle signal mode

其中，signal 参数表示要设定的目标信号，它通常为某个信号的全名（SIGINT）或者简称（去除‘SIG’后的部分，如 INT）；如果要指定所有信号，可以用 all 表示。

mode 参数用于明确 GDB 处理该目标信息的方式，其值可以是如下几个：

*   nostop：当信号发生时，GDB 不会暂停程序，其可以继续执行，但会打印出一条提示信息，告诉我们信号已经发生；
*   stop：当信号发生时，GDB 会暂停程序执行。
*   noprint：当信号发生时，GDB 不会打印出任何提示信息；
*   print：当信号发生时，GDB 会打印出必要的提示信息；
*   nopass（或者 ignore）：GDB 捕获目标信号的同时，不允许程序自行处理该信号；
*   pass（或者 noignore）：GDB 调试在捕获目标信号的同时，也允许程序自动处理该信号。

> 注意，当 GDB 捕获到信号并暂停程序执行的那一刻，程序是捕获不到信号的，只有等到程序继续执行时，信号才能被程序捕获。

接下来以一段 C 语言程序为例，为大家演示 GDB 调试器处理信号的过程。

```

#include <stdio.h>
#include <signal.h>
void display(){
    printf("http://c.biancheng.net/gdb/\n");
}
int main ()
{
    signal(SIGINT,display);
    while(1){
        sleep(1);
        printf("main\n");
    }
    return 0;
}
```

> 该程序存储在 ~/demo/main.c 文件中，并已编译为可供 GDB 调试的 main.exe 可执行程序。

前面已经讲过，SIGINT 信号是可以通过`Ctrl+c`组合键发出的，因此上面程序执行时，除非我们手动发出 SIGINT 信号，程序会马上执行 display() 函数，否则一直输出 "main" 字符串。

下面我们用 GDB 调试器调试 main.exe 程序：

(gdb) info signals SIGINT
Signal   Stop   Print  Pass to program  Description
SIGINT   Yes    Yes    No                        Interrupt
(gdb) r
Starting program: ~/demo/main.exe
main
main
^C
Program received signal SIGINT, Interrupt.
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb) 

可以看到，通过执行`info signals SIGINT`命令，我们调取出了当前 GDB 调试器对 SIGINT 信号处理方式的默认设定，即当 SIGINT 信号发生时，GDB 调试器会暂停程序执行，同时打印出必要的提示信息，并且不让程序捕获到该信号。由此，当程序执行过程中按下`Ctrl+c`组合键后，并没有执行 display() 函数，而是立即暂停了程序。

在此基础上，我们继续做如下调试：

(gdb) handle SIGINT nostop
SIGINT is used by the debugger.
Are you sure you want to change it? (y or n) y
Signal   Stop   Print  Pass to program  Description
SIGINT  No     Yes    No                        Interrupt
(gdb) handle SIGINT pass
SIGINT is used by the debugger.
Are you sure you want to change it? (y or n) y
Signal   Stop   Print  Pass to program  Description
SIGINT  No     Yes    Yes                        Interrupt
(gdb) continue
Continuing.
main
main
main
^C
Program received signal SIGINT, Interrupt.
http://c.biancheng.net/gdb/
main
^Z
Program received signal SIGTSTP, Stopped (user).
0x00000037ee2accc0 in __nanosleep_nocancel () from /lib64/libc.so.6
(gdb)

可以看到，通过将 SIGINT 信号 Stop 选项改为 No，将 Pass 选项改为 Yes，意味着当程序执行过程中发生 SIGINT 信号时，程序不再中断，并且可以捕获到此信号。因此，当程序执行过程中按下`Ctrl+c`组合键时，看到打印出了 "http://c.baincheng.net/gdb/" 数据。