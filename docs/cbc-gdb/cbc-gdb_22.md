# GDB 反向调试

> 原文：[`c.biancheng.net/view/8279.html`](http://c.biancheng.net/view/8279.html)

读到本节，我们已经学会了借助 GDB 调试器对代码进行单步调试和断点调试。这 2 种调试方法有一个共同的特点，即调试过程中代码一直都是“正向”执行的（从第一行代码执行到最后一行代码）。这就产生一个问题，如果调试过程中不小心多执行了一次 next、step 或者 continue 命令，又或者想再次查看刚刚程序执行的过程，该怎么办呢？

面对这种情况，很多读者想到的是借助 run 命令重新启动程序，还原之前所做的所有调试工作。的确，这种方式可以解决上面列举的类似问题，只不过比较麻烦。事实上，如果我们使用的是 7.0 及以上版本的 GDB 调试器，还有一种更简单的解决方法，即反向调试。

所谓反向调试，指的是临时改变程序的执行方向，反向执行指定行数的代码，此过程中 GDB 调试器可以消除这些代码所做的工作，将调试环境还原到这些代码未执行前的状态。

> 注意，目前 GDB 调试器对反向调试的功能支持还不完善，反向调试尚不能适用于所有的调试场景，本节后续会做详细讲解。

要想反向调试程序，我们需要学会使用相应的一些命令。表 1 为大家罗列了完成反向调试常用的一些命令，并且展示各个命令的使用格式和对应的功能。

表 1 GDB 反向调试的常用命令

| 命 令 | 功 能 |
| (gdb) record (gdb) record btrace | 让程序开始记录反向调试所必要的信息，其中包括保存程序每一步运行的结果等等信息。进行反向调试之前（启动程序之后），需执行此命令，否则是无法进行反向调试的。 |
| (gdb) reverse-continue (gdb) rc | 反向运行程序，直到遇到使程序中断的事件，比如断点或者已经退回到 record 命令开启时程序执行到的位置。 |
| (gdb) reverse-step | 反向执行一行代码，并在上一行代码的开头处暂停。和 step 命令类似，当反向遇到函数时，该命令会回退到函数内部，并在函数最后一行代码的开头处（通常为 return 0; ）暂停执行。 |
| (gdb) reverse-next | 反向执行一行代码，并在上一行代码的开头处暂停。和 reverse-step 命令不同，该命令不会进入函数内部，而仅将被调用函数视为一行代码。 |
| (gdb) reverse-finish | 当在函数内部进行反向调试时，该命令可以回退到调用当前函数的代码处。 |
| (gdb) set exec-direction mode | mode 参数值可以为 forward （默认值）和 reverse： 
*   forward 表示 GDB 以正常的方式执行所有命令；
*   reverse 表示 GDB 将反向执行所有命令，由此我们直接只用 step、next、continue、finish 命令来反向调试程序。注意，return 命令不能在 reverse 模式中使用。

 |

> 注意，表 1 中仅罗列了常用的一些命令，并且仅展示了各个命令最常用的语法格式。有关 GDB 调试器提供的更多支持反向调试的命令，以及各个命令不同的语法格式，感兴趣的读者可前往 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Reverse-Execution.html#Reverse-Execution)查看。

接下来以调试如下的 C 语言程序为例，给大家演示如何使用表 1 中的命令实现反向调试：

```

#include <stdio.h>
int main ()
{
    int n, sum;
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

程序存储在`~/demo/main.c`文件中，并以生成可供 GDB 调试的 main.exe 可执行文件。在此基础上，进行如下调试操作：

(gdb) b 6
Breakpoint 1 at 0x40047f: file main.c, line 6.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:6
6     sum = 0;
(gdb) record                                                       <-- 开启记录模式
(gdb) b 10 if n==10
Breakpoint 2 at 0x40048e: file main.c, line 10.
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:10
10         n = n + 1;
(gdb) p n                                                           <-- 执行至 n=10，sum=55
$1 = 10
(gdb) p sum
$2 = 55
(gdb) reverse-next                                            <-- 回退一步，暂停在第 9 行代码开头处
9         sum = sum + n;
(gdb) p n                                                           <-- 此时 n=10，sum=45
$3 = 10
(gdb) p sum
$4 = 45
(gdb) reverse-continue                                      <-- 反向执行代码，直至第 6 行，也就是开启记录的起始位置
Continuing.

No more reverse-execution history.
main () at main.c:6
6     sum = 0;
(gdb) p n                                                             <-- 回到了 n=1，sum=0 的时候
$5 = 1
(gdb) p sum
$6 = 0
(gdb)

当然，此示例中仅展示了 GDB 反向调试的部分功能，有关表 1 中的其它命令，读者可自行编写相应的测试程序进行测试，这里不再过多赘述。

#### GDB 调试功能尚不完善

要知道，GDB 反向调试的功能是 2006 年左右开始研发的，并于 7.0 版本正式发布。到目前为止，GDB 反向调试的功能还不太稳定，尚无法适用于所有的调试场景。

例如，对于打印到屏幕的数据并不会因为打印语句的回退而自动消失。换句话说，对于程序中出现的打印语句（例如 C 语言中的 printf() 输出函数），虽然可以进行反向调试，但已经输出到屏幕上的数据不会因反向调试而撤销。另外，反向调试也不适用于包含 I/O 操作的代码。

总的来说，尽管 GDB 调试功能尚不完善，但瑕不掩瑜，它能满足大部分场景的需要。在需要进行将代码反向执行时，读者可以先尝试使用 GDB 反向调试，其次再使用 run 命令重新开启调试。