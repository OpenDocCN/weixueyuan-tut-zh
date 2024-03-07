# GDB 单步调试程序

> 原文：[`c.biancheng.net/view/8232.html`](http://c.biancheng.net/view/8232.html)

《调用 GDB 调试器的几种方式》一节中提到，借助 next 命令可以控制 GDB 单步执行程序。所谓单步调试，就是通过一行一行的执行程序，观察整个程序的执行流程，进而尝试发现一些存在的异常或者 Bug。

根据实际场景的需要，GDB 调试器共提供了 3 种可实现单步调试程序的方法，即使用 next、step 和 until 命令。换句话说，这 3 个命令都可以控制 GDB 调试器每次仅执行 1 行代码，但除此之外，它们各自还有不同的功能。

本节就来一一给大家讲解这 3 个命令的功能和用法。讲解过程中，将以调试如下 C 语言程序为例：

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

此程序存储在`~/demo/main.c`源文件中（~ 表示当前用户的主目录），功能是根据用户输入的 num 值，输出 1²+2²+...+num² 的值。

## GDB next 命令

next 是最常用来进行单步调试的命令，其最大的特点是当遇到包含调用函数的语句时，无论函数内部包含多少行代码，next 指令都会一步执行完。也就是说，对于调用的函数来说，next 命令只会将其视作一行代码。

next 命令可以缩写为 n 命令，使用方法也很简单，语法格式如下：

(gdb) next count

参数 count 表示单步执行多少行代码，默认为 1 行。

举个例子：

(gdb) b 16
Breakpoint 2 at 0x40058b: file main.c, line 16.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 2, main () at main.c:16
16     int num =0;
(gdb) n 2                               <-- 单步执行 2 次
3
18     int result = myfunc(num);
(gdb) n
19     printf("%d", result);
(gdb) n
20     return 0;
(gdb)

可以看到，当程序单步执行第 18 行时，继续执行 next 指令，下一次将要执行的是第 19 行代码，而非 myfunc() 函数内部的代码。

## GDB step 命令

通常情况下，step 命令和 next 命令的功能相同，都是单步执行程序。不同之处在于，当 step 命令所执行的代码行中包含函数时，会进入该函数内部，并在函数第一行代码处停止执行。

step 命令可以缩写为 s 命令，用法和 next 命令相同，语法格式如下：

(gdb) step count

参数 count 表示一次执行的行数，默认为 1 行。

仍以 main.exe 为例：

(gdb) b 18
Breakpoint 1 at 0x4005ab: file main.c, line 18.
(gdb) r
Starting program: ~/demo/main.exe
Breakpoint 1, main () at main.c:18
18     int result = myfunc(num);
(gdb) step                                         <-- step 命令进入 myfunc() 函数内部执行
myfunc (num=0) at main.c:7
7     int i = 1;

可以看到，当程序暂停到包含 mufunc() 函数的代码行处时（此时该行代码尚未执行），如果使用 step 命令，则 GDB 在执行该函数代码的同时，会进入 mufunc() 函数内部，并暂停在函数内部的第一行代码处。反之如果使用 next 命令，则程序下一次会执行第 19 行代码，而不是第 7 行，这就是它们最大的不同之处。

## GDB until 命令

until 命令可以简写为 u 命令，有 2 种语法格式，如下所示：

1、(gdb) until
2、(gdb) until location

其中，参数 location 为某一行代码的行号。

不带参数的 until 命令，可以使 GDB 调试器快速运行完当前的循环体，并运行至循环体外停止。注意，until 命令并非任何情况下都会发挥这个作用，只有当执行至循环体尾部（最后一行代码）时，until 命令才会发生此作用；反之，until 命令和 next 命令的功能一样，只是单步执行程序。

以 main.exe 中 myfunc() 函数的循环为例：

(gdb) b 17
Breakpoint 1 at 0x1201: file main.c, line 17.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:17
17     scanf("%d", &num);
(gdb) u
3
18     int result = myfunc(num);
(gdb) step
myfunc (num=3) at main.c:7
7     int i = 1;
(gdb) u
8     int sum = 0;
(gdb) u
9     while(i <= num){
(gdb) u
10         sum += print(i);
(gdb) u
11         i++;
(gdb) u                                 <-- 执行 i++ 操作
9     while(i <= num){
(gdb) u                                 <-- 快速执行完循环体
13     return sum;
(gdb) p sum
$1 = 14

可以看到，这里当程序单步执行完第 11 行代码时，借助 until 命令快速执行完了整个循环体，并在第 13 行代码处停止执行。根据 p 命令输出的 num 变量的值可以确认，整个循环过程确定完整地执行完了。

until 命令还可以后跟某行代码的行号，以指示 GDB 调试器直接执行至指定位置后停止。举个例子：

(gdb) r
Starting program:~/demo/main.exe

Breakpoint 1, main () at main.c:17
17     scanf("%d", &num);
(gdb) until 19                                    <-- 执行至第 19 行停止
3
main () at main.c:19
19     printf("%d", result);
(gdb) p result
$3 = 14

可以看到，通过执行 until 19 命令，GDB 调试器直接从第 17 行代码处执行至指定的第 19 行。