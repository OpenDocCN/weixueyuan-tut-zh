# GDB 调试多线程程序

> 原文：[`c.biancheng.net/view/8261.html`](http://c.biancheng.net/view/8261.html)

顾名思义，多线程程序指的就是拥有多个（≥2）线程的程序，各个线程可以独立完成一项任务，更重要的是它们可以并发执行。这也就意味着完成相同的任务，多线程程序执行花费的时间往往会少于单线程程序。

但有得也有失，多线程程序的编写更容易产生异常或 Bug（例如线程之间因竞争同一资源发生了死锁、多个线程同时对同一资源进行读和写等等），调试多线程程序，往往需要耗费更多的精力。

庆幸的是，GDB 调试器不仅仅支持调试单线程程序，还支持调试多线程程序。本质上讲，使用 GDB 调试多线程程序的过程和调试单线程程序类似，不同之处在于，调试多线程程序需要监控多个线程的执行过程，进而找到导致程序出现问题的异常或 Bug，而调试单线程程序只需要监控 1 个线程。

表 1 罗列了 GDB 调试多线程程序时常用的命令以及它们各自的功能。

表 1 GDB 调试多线程程序常用命令

| 调试命令 | 功 能 |
| info threads | 查看当前调试环境中包含多少个线程，并打印出各个线程的相关信息，包括线程编号（ID）、线程名称等。 |
| thread id | 将线程编号为 id 的线程设置为当前线程。 |
| thread apply id... command | id... 表示线程的编号；command 代指 GDB 命令，如 next、continue 等。整个命令的功能是将 command 命令作用于指定编号的线程。当然，如果想将 command 命令作用于所有线程，id... 可以用 all 代替。 |
| break location thread id | 在 location 指定的位置建立普通断点，并且该断点仅用于暂停编号为 id 的线程。 |
| set scheduler-locking off&#124;on&#124;step | 默认情况下，当程序中某一线程暂停执行时，所有执行的线程都会暂停；同样，当执行 continue 命令时，默认所有暂停的程序都会继续执行。该命令可以打破此默认设置，即只继续执行当前线程，其它线程仍停止执行。 |

> 表 1 也仅罗列了 GDB 调试多线程程序的一部分常用命令，有关更多其他命令，读者可前往 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Thread-Stops.html#Thread-Stops)了解。

接下来，我将以调试如下 C 语言多线程程序为例，给大家详细讲解表 1 中这些命令的功能和用法：

```

#include <stdio.h>
#include <pthread.h>

void* thread_job(void*name)
{
    char * thread_name = (char*)name;
    printf("this is %s\n",thread_name);
    printf("http://c.biancheng.net\n");
}

int main()
{
    pthread_t tid1,tid2;
    pthread_create(&tid1, NULL, thread_job, "thread1_job");
    pthread_create(&tid2, NULL, thread_job, "thread2_job");
    pthread_join(tid1,NULL);
    pthread_join(tid2,NULL);
    printf("this is main\n");
    return 0;
}
```

此程序的存储位置为 ~/demo/main.c。可以看到，此程序中包含 3 个线程，分别为 main 主线程、tid1 子线程和 tid2 子线程。

需要注意的是，将此程序编译为可供 GDB 调试的可执行程序时，需执行如下命令：

gcc main.c -o main.exe -g -lpthread

因为 pthread 线程库并不属于 Linux 系统中的默认库，所以编译、链接时就需要为 gcc 命令附加 -lpthread 参数。

## GDB 查看所有线程

info threads 命令的功能有 2 个，既可以查看当前调试环境下存在的线程数以及各线程的具体信息，也可以通过指定线程的编号查看某个线程的具体信息。

info threads 命令的完整语法格式如下：

(gdb) info threads [id...]

其中，参数 id... 作为可选参数，表示要查看的线程编号，编号个数可以是多个。

以 main.exe 程序为例：

(gdb) b 6
Breakpoint 1 at 0x11d9: file main.c, line 6.
(gdb) r
Starting program: ~/demo/main.exe
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7ffff7d9f700 (LWP 54283)]      <--新线程
[New Thread 0x7ffff759e700 (LWP 54284)]     <--新线程
[Switching to Thread 0x7ffff7d9f700 (LWP 54283)]       <--该线程作为当前线程，因为它最先碰到断点

Thread 2 "main.exe" hit Breakpoint 1, thread_job (name=0x555555556027) at main.c:6
6     char * thread_name = (char*)name;
(gdb) info threads
  Id   Target Id                                                                 Frame
  1    Thread 0x7ffff7da0740 (LWP 54279) "main.exe"    __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2    Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"    thread_job (name=0x555555556027) at main.c:6
  3    Thread 0x7ffff759e700 (LWP 54284) "main.exe"    thread_job (name=0x555555556033) at main.c:6

> 其中 Id 列表示各个线程的编号（ID 号）；Target Id 列表示各个线程的标识符；Frame 列打印各个线程执行的有关信息，例如线程名称，线程暂停的具体位置等。

要知道，使用 GDB 调试多线程程序时，同一时刻我们调试的焦点都只能是某个线程，被称为当前线程。整个调试过程中，GDB 调试器总是会从当前线程的角度为我们打印调试信息。如上所示，当执行 r 启动程序后，GDB 编译器自行选择标识号为 LWP 54283（编号为 2）的线程作为当前线程，则随后打印的暂停运行的信息就与该线程有关，而没有打印出编号为 1 和 3 的暂停信息。

GDB 调试器为了方便用户快速识别出当前线程，执行 info thread 命令后，Id 列前标有 * 号的线程即为当前线程。

> 这里要提醒大家的，我们输入的调试命令并不仅仅作用于当前线程，例如 continue、next 等，默认情况下它们作用于所有线程。

## GDB 调整当前线程

用 GDB 调试多线程程序的过程中，根据需要可以随时对当前线程进行调整，这就需要用到 thead id 命令。

thread id 命令用于将编号为 id 的线程设定为当前线程。举个例子：

(gdb) thread 3
[Switching to thread 3 (Thread 0x7ffff759e700 (LWP 54284))]    <--切换当前线程
#0  thread_job (name=0x555555556033) at main.c:6
6     char * thread_name = (char*)name;
(gdb) info threads
  Id   Target Id                                                                Frame
  1    Thread 0x7ffff7da0740 (LWP 54279) "main.exe"   __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
  2    Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"    thread_job (name=0x555555556027) at main.c:6
* 3    Thread 0x7ffff759e700 (LWP 54284) "main.exe"   thread_job (name=0x555555556033) at main.c:6

可以看到，改变当前线程的同时，GDB 调试器为我们打印出了该线程暂停执行的具体信息。再次执行 info threads 命令可以看到，编号为 3 的线程确实成为了新的当前线程。

## GDB 执行特定线程

如果想单独控制某一线程进行指定的操作，可以借助 thread apply id... command 命令实现：

(gdb) thread apply id... command

参数 id... 表示要控制的目标线程的编号，编号个数可以是多个。如果想控制所有线程，可以用 all 代替书写所有线程的编号；参数 command 表示要目标线程执行的操作，例如 next、continue 等。

举个例子：

(gdb) info threads
  Id   Target Id                                                                 Frame
  1    Thread 0x7ffff7da0740 (LWP 54279) "main.exe"    __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
  2    Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"     thread_job (name=0x555555556027) at main.c:6
* 3    Thread 0x7ffff759e700 (LWP 54284) "main.exe"    thread_job (name=0x555555556033) at main.c:6
(gdb) thread apply 2 next

Thread 2 (Thread 0x7ffff759e700 (LWP 54284)):
[Switching to Thread 0x7ffff759e700 (LWP 54284)]   <-- 由于 3 号线程暂停执行，所以切换 3 号线程作为当前线程

Thread 3 "main.exe" hit Breakpoint 1, thread_job (name=0x555555556033) at main.c:6
6     char * thread_name = (char*)name;
(gdb) thread apply 2 next

Thread 2 (Thread 0x7ffff7d9f700 (LWP 54283)):
7     printf("this is %s\n",thread_name);
(gdb) 

如上所示，当我们调用 thread apply 2 next 命令对 2 号线程进行逐步调试时，3 号线程也会运行，这是为什么呢？这和 GDB 调试器的调试机制有关。

默认情况下，无论哪个线程暂停执行，其它线程都会随即暂停；反之，一旦某个线程启动（借助 next、step、continue 命令），其它线程也随即启动。GDB 调试默认的这种调试模式（称为全停止模式），一定程序上可以帮助我们更好地监控程序中各个线程的执行。

> 注意，当对某个线程进行单步调试时，其它线程也会随即执行和停止，但执行的往往不只是一行代码，可能是多行代码。

## GDB 为特定线程设置断点

当调试环境中拥有多个线程时，我们可以选择为特定的线程设置断点，该断点仅对指定线程有效。

为特定的某个线程设置断点，可以使用如下命令：

(gdb) break location thread id
(gdb) break location thread id if...

location 表示设置断点的具体位置；id 表示断点要作用的线程的编号；if... 参数作用指定断点激活的条件，即只有条件符合时，断点才会发挥作用。

> 默认情况下，当某个线程执行遇到断点时，GDB 调试器会自动将该线程作为当前线程，并提示用户 "[Switching to Thread n]"，其中 n 即为新的当前线程。

举个例子：

(gdb) break 7 thread 3
Breakpoint 5 at 0x5555555551e1: file main.c, line 7.
(gdb) info break
Num     Type           Disp Enb Address            What
4       breakpoint     keep y   0x00005555555551d9 in thread_job at main.c:6 breakpoint already hit 1 time
5       breakpoint     keep y   0x00005555555551e1 in thread_job at main.c:7 thread 3 stop only in thread 3

可以看到，我们在第 7 行代码处为 3 号线程单独设置了一个普通断点，该断点仅对 3 号线程有效。

> 有关在设置断点的基础为其添加触发条件，我已经在《GDB 条件断点》一节做了详细的讲解，这里不再重复赘述。

## GDB 设置线程锁

前面提到，使用 GDB 调试多线程程序时，默认的调试模式为：一个线程暂停运行，其它线程也随即暂停；一个线程启动运行，其它线程也随即启动。要知道，这种调试机制确实能帮我们更好地监控各个线程的“一举一动”，但并非适用于所有场景。

一些场景中，我们可能只想让某一特定线程运行，其它线程仍维持暂停状态。要想达到这样的效果，就需要借助 set scheduler-locking 命令。 此命令可以帮我们将其它线程都“锁起来”，使后续执行的命令只对当前线程或者指定线程有效，而对其它线程无效。

set scheduler-locking 命令的语法格式如下：

(gdb) set scheduler-locking mode

其中，参数 mode 的值有 3 个，分别为 off、on 和 step，它们的含义分别是：

*   off：不锁定线程，任何线程都可以随时执行；
*   on：锁定线程，只有当前线程或指定线程可以运行；
*   step：当单步执行某一线程时，其它线程不会执行，同时保证在调试过程中当前线程不会发生改变。但如果该模式下执行 continue、until、finish 命令，则其它线程也会执行，并且如果某一线程执行过程遇到断点，则 GDB 调试器会将该线程作为当前线程。

举个例子：

(gdb) info threads
  Id   Target Id                                                                 Frame
  1    Thread 0x7ffff7da0740 (LWP 54279) "main.exe"    __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2    Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"     thread_job (name=0x555555556027) at main.c:6
  3    Thread 0x7ffff759e700 (LWP 54284) "main.exe"    thread_job (name=0x555555556033) at main.c:6
(gdb) set scheduler-locking on
(gdb) next
7     printf("this is %s\n",thread_name);
(gdb) info threads
  Id   Target Id                                                                 Frame
  1    Thread 0x7ffff7da0740 (LWP 54279) "main.exe"    __pthread_clockjoin_ex (threadid=140737351644928, thread_return=0x0, clockid=<optimized out>, abstime=<optimized out>, block=<optimized out>) at pthread_join_common.c:145
* 2    Thread 0x7ffff7d9f700 (LWP 54283) "main.exe"     thread_job (name=0x555555556027) at main.c:7
  3    Thread 0x7ffff759e700 (LWP 54284) "main.exe"    thread_job (name=0x555555556033) at main.c:6
(gdb)

可以看到，通过执行 set scheduler-locking on 命令，接下来的 next 命令只对当前线程（2 号线程）有效，其它线程仍保持原有的暂停状态。

同时，我们可以通过执行 show scheduler-locking 命令，查看各个线程锁定的状态，例如：

(gdb) show scheduler-locking
Mode for locking scheduler during execution is "on".

显然，当前 set scheduler-locking 命令的值为 on。