# GDB 断点调试详解

> 原文：[`c.biancheng.net/view/8203.html`](http://c.biancheng.net/view/8203.html)

前面利用 3 节的内容，分别介绍了 GDB 调试器支持在被调试程序中打断点的 3 种方法，即 break、watch 以及 catch 命令。在此基础上，本节给大家讲解：如何借助断点对程序进行调试？

通过在程序的适当位置打断点，观察程序执行至该位置时某些变量（或表达式）的值，进而不断缩小导致程序出现异常或 Bug 的语句的搜索范围，并最终找到，整个过程就称为断点调试。

值得一提的是，整个断点调试的过程，除了要借助 break、watch 或者 catch 命令以外，还要借助其它一些命令，例如在前面章节中，我们已经使用过的 print 命令（查看变量的值）、continue 命令（使程序继续执行）等。

表 1 罗列了断点调试程序过程中，常用的一些命令以及各自的含义。

表 1 GDB 断点调试常用命令

| 命令（缩写） | 功 能 |
| run（r） | 启动或者重启一个程序。 |
| list（l） | 显示带有行号的源码。 |
| continue（c） | 让暂停的程序继续运行。 |
| next（n） | 单步调试程序，即手动控制代码一行一行地执行。 |
| step（s） | 如果有调用函数，进入调用的函数内部；否则，和 next 命令的功能一样。 |
| until（u） until location（u location） | 当你厌倦了在一个循环体内单步跟踪时，单纯使用 until 命令，可以运行程序直到退出循环体。 until n 命令中，n 为某一行代码的行号，该命令会使程序运行至第 n 行代码处停止。 |
| finish（fi） | 结束当前正在执行的函数，并在跳出函数后暂停程序的执行。 |
| return（return） | 结束当前调用函数并返回指定值，到上一层函数调用处停止程序执行。 |
| jump（j） | 使程序从当前要执行的代码处，直接跳转到指定位置处继续执行后续的代码。 |
| print（p） | 打印指定变量的值。 |
| quit（q） | 退出 GDB 调试器。 |

> 其中，next、step 以及 until 命令，已经在 《GDB 单步调试》一节中做了详细介绍，本节不再赘述。另外，表 1 中罗列的这些命令读者无需死记硬背，因为实际使用 GDB 调试器时，它们都会常常用到，熟能生巧。

为了搞清楚表 1 中这些命令的功能和用法，这里仍以上节创建的 C 语言程序为例：

```

#include <stdio.h>
int print(int num){
    int ret = num * num;
    return ret;
}
int myfunc(int num){
    int i = 1;
    int sum = 0;
    while(i <= num){
        sum += print(i);
        i++;
    }
    return sum;
}
int main(){
    int num =0;
    scanf("%d", &num);
    int result = myfunc(num);
    printf("%d", result);
    return 0;
}
```

此程序存储在`~/demo/main.c`源文件中（~ 表示当前用户的主目录）。

1) 首先，表 1 中 run、continue、list、next、print 以及 quit 命令的用法都非常简单，唯一需要注意的一点是，run 命令除了可以启动程序的执行，还可以在任何时候重新启动程序。

例如，以 main.c 为例：

[root@bogon demo]#  gdb main.exe -q
Reading symbols from main.exe...
(gdb) l                     <-- list 命令的缩写，罗列出带有行号的源码
1 #include <stdio.h>
2 int print(int num){
3     int ret = num * num;
4     return ret;
......
15 int main(){
16     int num =0;
17     scanf("%d", &num);
18     int result = myfunc(num);
19     printf("%d", result);
20     return 0;
(gdb)
21 }
(gdb) b 16                         <-- break 命令的缩写，在程序第 16 行处打普通断点
Breakpoint 1 at 0x11fa: file main.c, line 16.
(gdb) r                               <-- run 命令的缩写，启动程序
Starting program: /home/test/demo/main.exe
Breakpoint 1, main () at main.c:16
16     int num =0;
(gdb) next                          <-- 单步执行程序，即执行一行代码
17     scanf("%d", &num);
(gdb) next                          <-- 继续单步执行，此时需要输入一个整数传递给 num
3
18     int result = myfunc(num);
(gdb) p num                       <-- print 命令的缩写，显示 num 的值
$1 = 3                                 <-- $1 表示 num 变量所在存储区的名称，这里指的是 num 的值为 3
(gdb) n                                <-- 继续单步执行
19     printf("%d", result);
(gdb) n                                <-- 单步执行
20     return 0;
(gdb) q                                <-- 退出调试
A debugging session is active.

Inferior 1 [process 4576] will be killed.

Quit anyway? (y or n) y        <-- 由于程序执行尚未结束，GDB 会进行再次确认
[root@bogon demo]#

> 事实上，以上很多命令还有其它的语法格式，只是不常用，这里不再过多赘述，感兴趣的读者可自行查阅 [GDB 官网手册](https://sourceware.org/gdb/current/onlinedocs/gdb/index.html#SEC_Contents)。

接下来，重点给大家介绍表 1 中另外几个命令的用法。

## GDB finish 和 return 命令

实际调试时，在某个函数中调试一段时间后，可能不需要再一步步执行到函数返回处，希望直接执行完当前函数，这时可以使用 finish 命令。与 finish 命令类似的还有 return 命令，它们都可以结束当前执行的函数。

finish 命令和 return 命令的区别是，finish 命令会执行函数到正常退出；而 return 命令是立即结束执行当前函数并返回，也就是说，如果当前函数还有剩余的代码未执行完毕，也不会执行了。除此之外，return 命令还有一个功能，即可以指定该函数的返回值。

仍以 main.exe 为例，如下演示了 finish 命令的用法：

(gdb) b 18
Breakpoint 1 at 0x4005ab: file main.c, line 18.
(gdb) r
Starting program: ~/demo/main.exe
3

Breakpoint 1, main () at main.c:18
18     int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7     int i = 1;
(gdb) n
8     int sum = 0;
(gdb) n
9     while(i <= num){
(gdb) finish
Run till exit from #0  myfunc (num=3) at main.c:9
0x00000000004005b5 in main () at main.c:18
18     int result = myfunc(num);
Value returned is $1 = 14
(gdb) 

可以看到，当程序运行至第 9 行处使用 finish 命令，GDB 调试器会执行完 myfunc() 函数中的剩余代码，并在执行完函数后停止。接下来重新执行程序，观察 return 命令的功能：

(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: ~/demo/main.exe
3

Breakpoint 1, main () at main.c:18
18     int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7     int i = 1;
(gdb) n
8     int sum = 0;
(gdb) n
9     while(i <= num){
(gdb) return 5
Make myfunc return now? (y or n) y
#0  0x00000000004005b5 in main () at main.c:18
18     int result = myfunc(num);
(gdb) n
19     printf("%d", result);
(gdb) p result
$3 = 5

可以看到，同样程序执行至第 9 行，借助 return 命令会立即终止执行 myfunc() 函数，同时手动指定该函数的返回值为 5。因此，最终 result 变量的值为 5，而不再是 14。

## GDB jump 命令

jump 命令的功能是直接跳到指定行继续执行程序，其语法格式为：

(gdb) jump location

其中，location 通常为某一行代码的行号。

也就是说，jump 命令可以略过某些代码，直接跳到 location 处的代码继续执行程序。这意味着，如果你跳过了某个变量（对象）的初始化代码，直接执行操作该变量（对象）的代码，很可能会导致程序崩溃或出现其它 Bug。另外，如果 jump 跳转到的位置后续没有断点，那么 GDB 会直接执行自跳转处开始的后续代码。

举个例子：

(gdb) b 16
Breakpoint 1 at 0x40058b: file main.c, line 16.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:16
16     int num = 0;
(gdb) jump 19
Continuing at 0x4005b8.
0
Program exited normally.

可以看到，由于借助 jump 指令跳过了 result 变量的初始化过程，因此 result 变量的值为 0（或者垃圾值）。

> 注意，从第 16 行直接跳到第 19 行执行，并不意味着 result 变量不能使用。因为对于可执行文件而言，并不存在 num、result 这些变量名，它们都已经被转化为了地址（确定地说是偏移地址），并且程序在执行时，位于 main() 函数中的所有变量的存储地址都会被确定。也就是说，当我们跳到第 19 行输出 result 的值时，实际上是取其存储地址中的数据，只不过由于 result 没有初始化，所以最终结果值可能为 0，也可能为垃圾值。