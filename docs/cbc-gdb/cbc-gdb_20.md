# GDB 后台（异步）执行调试命令

> 原文：[`c.biancheng.net/view/8272.html`](http://c.biancheng.net/view/8272.html)

前面章节中，我们已经接触了很多调试命令，如 run（r）、continue（c）、next（n）等，借助它们即可操控 GDB 调试目标程序。有些读者可能已经注意到，这些调试命令在执行过程中，是无法使用其它 GDB 调试命令的，换句话说，只有当一个调试命令执行结束后，(gdb) 命令提示符才会出现，我们才能执行下一个调试命令。

事实上，对于某些调试命令，GDB 调试器提供有 2 种执行方式：

*   同步执行：“一个一个”的执行，即必须等待前一个命令执行完毕，才能执行下一个调试命令。
*   后台执行：又称“异步执行”，即当某个调试命令开始执行时，(gdb) 命令提示符会立即出现，我们无需等待前一个命令执行完毕就可以继续执行下一个调试命令。 

以后台（异步）的方式执行一个调试命令，其语法格式如下：

(gdb) command&

其中，command 表示就是要执行的调试命令。command 和 & 之间不需要添加空格。

显然，通过在目标命令的后面添加一个 '&' 字符，即可使该命令以后台的方式执行。例如，continue 命令的异步执行版本为`continue&` 或者`c&`。

表 1 罗列了支持后台执行的一些常用的调试命令。

表 1 GDB 支持后台执行的调试命令

| 调试命令 | 含 义 |
| run（r） | 启动被调试的程序。有关此命令的具体用法，读者可阅读《GDB run 命令》一节。 |
| attach | 调试处于运行着的的程序。 |
| step | 单步调试程序。有关此命令的具体用法，可阅读《GDB 单步调试程序》一节。 |
| stepi | 执行一条机器指令。 |
| next(n) | 单步调试程序。有关此命令的具体用法，可阅读《GDB 单步调试程序》一节。 |
| nexti | 执行一条机器指令，其与 stepi 命令的区别，类似于 step 和 next 命令的区别。 |
| continue | 继续执行程序。 |
| finish | 结束当前正在执行的函数。有关此命令的用法，可阅读《GDB 断点调试》一节。 |
| until（u） | 快速执行完当前的循环体。有关此命令的用法，可阅读《GDB 单步调试程序》一节。 |

> 注意，并非所有的调试命令都支持异步执行。如果目标调试命令不支持后台执行的形式，当我们尝试使用“命令名+&”的方式执行该命令时，GDB 调试器会提示类似“Function "&" not defined.”的错误信息。

后台执行命令异步调试程序的方法，多用于 non-stop 模式中。虽然 all-stop 模式中也可以使用，但在前一个异步命令未执行完毕前，仍旧不能执行其它命令。

## GDB interrupt 命令：暂停后台线程执行

对于在后台处于执行状态的线程，可以使用 interrupt 命令将其中断。以调试《GDB non-stop 模式》一节给出的多线程程序为例：

(gdb) l
1 #include <stdio.h>
2 #include <pthread.h>
3
4 static void *thread1_job()
5 {
6     printf("this is 1\n");
7 }
8 static void *thread2_job()
9 {
10     printf("this is 2\n");
(gdb)
11 }
12
13 int main()
14 {
15     pthread_t tid1,tid2;
16     pthread_create(&tid1, NULL, thread1_job, NULL);
17     pthread_create(&tid2, NULL, thread2_job, NULL);
18     pthread_join(tid1,NULL);
19     pthread_join(tid2,NULL);
20     printf("this is main\n");
(gdb)
21     return 0;
22 }
(gdb) b 6
Breakpoint 1 at 0x11b1: file main.c, line 6.
(gdb) set non-stop on                                             <-- 以 non-stop 模式调试程序
(gdb) r
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 57816)]
[New Thread 0x7ffff759e700 (LWP 57817)]
this is 2

Thread 2 "main.exe" hit Breakpoint 1, thread1_job () at main.c:6
6     printf("this is 1\n");
(gdb) [Thread 0x7ffff759e700 (LWP 57817) exited]

(gdb) info threads
  Id   Target Id                                                                Frame
* 1    Thread 0x7ffff7da0740 (LWP 57812) "main.exe"  (running)
   2    Thread 0x7ffff7d9f700 (LWP 57816) "main.exe"   thread1_job ()  at main.c:6
(gdb)

可以看到，当以 non-stop 模式调试程序时，由于我们仅在第 6 行代码处打上了断点，因此启动程序中，仅存在 2 个线程（1 个线程执行完毕后结束了），并且 1 号线程是一直在后台执行着的。

借助 interrupt 命令，我们可以将 1 号线程暂停执行：

(gdb) interrupt
(gdb)
Thread 1 "main.exe" stopped.
......
(gdb) info threads
  Id   Target Id                                                                Frame
* 1    Thread 0x7ffff7da0740 (LWP 57812) "main.exe"  ......at pthread_join_common.c:145
  2    Thread 0x7ffff7d9f700 (LWP 57816) "main.exe"   hread1_job () at main.c:6
(gdb)

可以看到，1 号线程停止了运行。

> 注意，在 all-stop 模式下，interrupt 命令作用于所有线程，即该命令可以令整个程序暂停执行；而在 non-stop 模式下，interrupt 命令仅作用于当前线程。 如果想另其作用于所有线程，可以执行 interrupt -a 命令。