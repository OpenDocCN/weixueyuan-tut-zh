# GDB print 和 display 命令：查看变量的值

> 原文：[`c.biancheng.net/view/8238.html`](http://c.biancheng.net/view/8238.html)

前面章节中提到，使用 GDB 调试程序，最常用的方法是：单步调试或者断点调试程序，期间通过查看某个变量或者表达式的值，判断当前程序的执行过程是否正确，不断缩小异常或 Bug 位于代码中的范围，最终找到并修复。

对于在调试期间查看某个变量或表达式的值，GDB 调试器提供有 2 种方法，即使用 print 命令或者 display 命令。本节就对这 2 个命令的功能和用法做详细的讲解，整个讲解过程将以调试如下 C 语言程序为例：

```

#include <stdio.h>
int main(){
    int num,result=0,i=0;
    scanf("%d", &num);
    while(i<=num){
        result += i;
        i++;
    }  
    printf("result=%d\n", result);
    return 0;
}
```

此程序存储在`~/demo/main.c`文件中（`~` 代指当前系统登录用户的主目录），并且已经其编译为可供 GDB 调试的 main.exe 可执行文件：

[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c  main.exe

## GDB print 命令

前面章节中，我们已经多次接触并使用了 print 命令，它的功能就是在 GDB 调试程序的过程中，输出或者修改指定变量或者表达式的值。

> 所谓表达式，简单理解就是由多个变量构成的式子。例如 a、b、c 为单个变量，a+b、a+b*c 即为表达式。

print 命令可以缩写为 p，最常用的语法格式如下所示：

(gdb) print num
(gdb) p num

其中，参数 num 用来代指要查看或者修改的目标变量或者表达式。

以调试 main.exe 为例：

[root@bogon demo]# gdb main.exe -q
Reading symbols from ~/demo/main.exe...done.
(gdb) l
1 #include <stdio.h>
2 int main(){
3     int num,result=0,i=0;
4     scanf("%d", &num);
5     while(i<=num){
6         result += i;
7         i++;
8     }  
9     printf("result=%d\n", result);
10     return 0;
(gdb)
11 }
(gdb) b 3
Breakpoint 1 at 0x40053c: file main.c, line 3.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 1, main () at main.c:4
4     scanf("%d", &num);
(gdb) n
3
5     while(i<=num){
(gdb) p num                                 <--输出 num 的值
$1 = 3
(gdb) p num=4                             <-- 修改 num 的值为 4
$2 = 4
(gdb) b 9
Breakpoint 2 at 0x400569: file main.c, line 9.
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:9
9     printf("result=%d\n", result);
(gdb) p result                                 <-- 输出 result 的值
$2 = 10
(gdb) p result=20                           <-- 修改 result 的值
$2 = 20
(gdb) c
Continuing.
result=20

Program exited normally.
(gdb)

可以看到，调试 main.exe 的过程中，我们先后使用 p 命令输出了程序中 num 和 result 变量的值，同时还使用该命令对它们的值做了修改。

> print 命令还有更高级的用法，后续会给读者做详细的讲解。

## GDB display 命令

和 print 命令一样，display 命令也用于调试阶段查看某个变量或表达式的值，它们的区别是，使用 display 命令查看变量或表达式的值，每当程序暂停执行（例如单步执行）时，GDB 调试器都会自动帮我们打印出来，而 print 命令则不会。

也就是说，使用 1 次 print 命令只能查看 1 次某个变量或表达式的值，而同样使用 1 次 display 命令，每次程序暂停执行时都会自动打印出目标变量或表达式的值。因此，当我们想频繁查看某个变量或表达式的值从而观察它的变化情况时，使用 display 命令可以一劳永逸。

display 命令没有缩写形式，常用的语法格式如下 2 种：

(gdb) display expr
(gdb) display/fmt expr

其中，expr 表示要查看的目标变量或表达式；参数 fmt 用于指定输出变量或表达式的格式，表 1 罗列了常用的一些 fmt 参数。

表 1 /fmt 常用的值

| /fmt | 功 能 |
| /x | 以十六进制的形式打印出整数。 |
| /d | 以有符号、十进制的形式打印出整数。 |
| /u | 以无符号、十进制的形式打印出整数。 |
| /o | 以八进制的形式打印出整数。 |
| /t | 以二进制的形式打印出整数。 |
| /f | 以浮点数的形式打印变量或表达式的值。 |
| /c | 以字符形式打印变量或表达式的值。 |

> 注意，display 命令和 /fmt 之间不要留有空格。以 /x 为例，应写为 (gdb)display/x expr。

仍以调试 main.exe 为例：

(gdb) b 4
Breakpoint 1 at 0x40053c: file main.c, line 4.
(gdb) b 9
Breakpoint 2 at 0x400569: file main.c, line 9.
(gdb) r
Starting program: /root/demo/main.exe

Breakpoint 1, main () at main.c:4
4     scanf("%d", &num);
(gdb) display num
1: num = 32767
(gdb) display/t result
2: /t result = 0
(gdb) n
3
5     while(i<=num){
2: /t result = 0
1: num = 3
(gdb) c
Continuing.

Breakpoint 2, main () at main.c:9
9     printf("result=%d\n", result);
2: /t result = 110
1: num = 3
(gdb) c
Continuing.
result=6

Program exited normally.
(gdb)

可以看到，使用 display 命令查看 num 和 result 变量值时，不仅在执行该命令的同时会看到目标变量的值，后续每次程序停止执行时，GDB 调试器都会将目标变量的值打印出来。

事实上，对于使用 display 命令查看的目标变量或表达式，都会被记录在一张列表（称为自动显示列表）中。通过执行`info dispaly`命令，可以打印出这张表：

(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:      y      /t result
1:      y      num

其中，各列的含义为：

*   Num 列为各变量或表达式的编号，GDB 调试器为每个变量或表达式都分配有唯一的编号；
*   Enb 列表示当前各个变量（表达式）是处于激活状态还是禁用状态，如果处于激活状态（用 y 表示），则每次程序停止执行，该变量的值都会被打印出来；反之，如果处于禁用状态（用 n 表示），则该变量（表达式）的值不会被打印。
*   Expression 列：表示查看的变量或表达式。

对于不需要再打印值的变量或表达式，可以将其删除或者禁用。

1) 通过执行如下命令，即可删除自动显示列表中的变量或表达式：

(gdb) undisplay num...
(gdb) delete display num...

参数 num... 表示目标变量或表达式的编号，编号的个数可以是多个。

举个例子：

(gdb) undisplay 1
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:      y      /t result
(gdb)

可以看到，借助 undisplay 命令成功删除了编号为 1 的 num 变量。

2) 通过执行如下命令，可以禁用自动显示列表中处于激活状态下的变量或表达式：

(gdb) disable display num...

num... 表示要禁用的变量或表达式的编号，编号的个数可以是多个，表示一次性禁用多个变量或表达式

举个例子：

(gdb) disable display 2
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:      n      /t result
(gdb) 

可以看到，编号为 2 的 result 变量的 Enb 由 y 变成了 n。处于禁用状态的变量或表达式，程序停止执行时将不再自动打印出它们的值。

当然根据需要，也可以激活当前处于禁用状态的变量或表达式，执行如下命令即可：

(gdb) enable display num...

参数 num... 表示要激活的变量或表达式的编号，编号的个数可以是多个，表示一次性激活多个变量或表达式。

举个例子：

(gdb) enable display 2
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:   y  /t result
(gdb) 

总的来说，每次程序停止执行时，GDB 调试器会将自动显示列表中处于激活状态下的变量或表达式的值打印出来，display 命令可以实现在查看目标变量或表达式的值的同时，将其添加到自动显示列表中，而 print 命令则只会打印出目标变量或表达式的值。