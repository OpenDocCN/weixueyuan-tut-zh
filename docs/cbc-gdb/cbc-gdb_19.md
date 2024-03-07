# GDB non-stop 模式详解

> 原文：[`c.biancheng.net/view/8270.html`](http://c.biancheng.net/view/8270.html)

《GDB 调试多线程程序》一节提到，对于调试多线程程序，GDB 默认采用的是 all-stop 模式，即只要有一个线程暂停执行，所有线程都随即暂停。这种调试模式可以适用于大部分场景的需要，借助适当数量的断点，我们可以清楚地监控到各个线程的具体执行过程。

但在某些场景中，我们可能需要调试个别的线程，并且不想在调试过程中，影响其它线程的运行。这种情况下，可以将 GDB 的调试模式由 all-stop 模式更改为 non-stop 模式，该模式下调试多线程程序，当某一线程暂停运行时，其它线程仍可以继续执行。

> 注意，只有 7.0 版本以上的 GDB 调试器，才支持 non-stop 模式。如果 GDB 版本不满足需求，可以阅读《GDB 下载和安装教程》一节安装高版本的 GDB。

也就是说，non-stop 模式下可以进行 all-stop 模式无法做到的调试工作，例如：

*   保持其它线程继续执行的状态下，单独调试某个线程；
*   在所有线程都暂停执行的状态下，单步调试某个线程；
*   单独执行多个线程等等。

另外还有一点和 all-stop 模式不同的是，在 all-stop 模式下，continue、next、step 命令的作用对象并不是当前线程，而是所有的线程；但在 non-stop 模式下，continue、next、step 命令只作用于当前线程。

> 在 non-stop 模式下，如果想要 continue 命令作用于所有线程，可以为 continue 命令添加一个 -a 选项，即执行 continue -a 或者 c -a 命令，即可实现令所有线程继续执行的目的。

那么，GDB 调试多线程程序时，怎样才能由 all-stop 模式转换到 non-stop 模式呢？很简单，未启动程序前执行如下命令即可：

(gdb) set non-stop mode

其中，mode 参数的值有 2 种，分别是 on 和 off，on 表示启用 non-stop 模式；off 表示禁用 non-stop 模式。

为了向读者展示 non-stop 模式的功能，这里以调试如下的 C 语言程序为例：

```

#include <stdio.h>
#include <pthread.h>

static void *thread1_job()
{
    printf("this is 1\n");
}
static void *thread2_job()
{
    printf("this is 2\n");
}

int main()
{
    pthread_t tid1,tid2;
    pthread_create(&tid1, NULL, thread1_job, NULL);
    pthread_create(&tid2, NULL, thread2_job, NULL);
    pthread_join(tid1,NULL);
    pthread_join(tid2,NULL);
    printf("this is main\n");
    return 0;
}
```

此程序存储于 ~/demo/main.c 文件中。可以看到，程序中包含有 3 个线程，分别为 main 主线程、thread1_job 子线程和 thread2_job 子线程。

注意，将 main.c 文件编译为供 GDB 调试的可执行文件时，需执行如下命令：

[root@bogon demo]# gcc main.c -o main.exe -g -lpthread
[root@bogon demo]# ls
main.c   main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...
(gdb) 

因为 pthread 线程库并不属于 Linux 系统中的默认库，所以编译、链接时就需要为 gcc 命令附加 -lpthread 参数。

在此基础上，下面开始为大家演示在 non-stop 模式下调试多线程程序：

(gdb) set non-stop on                               <-- 开启 non-stop 模式
(gdb) b 6                                                    <-- 在 thread1_job 线程执行的 thread1_job 函数中设置断点
Breakpoint 1 at 0x11b1: file main.c, line 6.
(gdb) r                                                         <-- 开始执行程序
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 55442)]

Thread 2 "main.exe" hit Breakpoint 1, thread1_job () at main.c:6
6     printf("this is 1\n");                               <-- thread1_job 线程受断点影响暂停
(gdb) [New Thread 0x7ffff759e700 (LWP 55443)]
this is 2
[Thread 0x7ffff759e700 (LWP 55443) exited]
Quit                                                                <-- thread2_job 一直执行至结束。           
(gdb) info threads
  Id   Target Id                                    Frame
* 1    Thread 0x7ffff7da0740 (LWP 55438) "main.exe" (running)
  2    Thread 0x7ffff7d9f700 (LWP 55442) "main.exe" thread1_job () at main.c:6

可以看到，如果在 all-stop 模式下，thread1_job 线程的暂停执行势必会导致 main 主线程和 thread2_job 线程暂停执行；但在 non-stop 模式下却完全相反，thread1_job 线程的暂停，并未影响到 main 主线程和 thread2_job 线程，其中 thread2_job 线程执行完毕后自动退出，而 main 主线程一直在运行（等待 thread1_job 线程执行结束）。

另外通过这个实例，还可以发现一点 non-stop 模式和 all-stop 模式的不同。在 all-stop 模式下，当某一线程暂停执行时，GDB 调试器会自行将其切换为当前线程；而在 non-stop 模式下不会。这也就解释了上面实例中，当 thread1_job 因断点暂停时当前线程仍为 main.exe 主线程。

除此之外，通过执行 show non-stop 命令，可以查看 non-stop 模式是否开启。例如：

(gdb) show non-stop
Controlling the inferior in non-stop mode is on.

注意，借助 set non-stop 或者 show non-stop 命令，也只能判断出 non-stop 模式是否开启，并不意味着当前执行的程序一定以 non-stop 模式运行。原因很简单，并非所有的调试环境都支持 non-stop 模式，即便借助 set non-step 命令启动了该模式，GDB 调试器也可能会自动切换至 all-stop 模式。