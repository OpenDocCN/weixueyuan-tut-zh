# GDB 禁用和删除断点

> 原文：[`c.biancheng.net/view/8219.html`](http://c.biancheng.net/view/8219.html)

我们知道，GDB 调试器支持 3 种断点，分别为普通断点（用 break 命令创建）、观察断点（用 watch 命令建立）以及捕捉断点（用 catch 命令建立）。并且如果需要的话，我们可以在被调试程序中打多个断点，甚至于 GDB 允许在同一位置打多个断点。

这就产生一个问题，如果之前建立的断点不再需要或者暂时不需要，该如何删除或者禁用呢？常用的方式有 2 种：

1.  使用 quit 命令退出调试，然后重新对目标程序启动调试，此方法会将消除上一次调试操作中建立的所有断点；
2.  使用专门删除或禁用断点的命令，既可以删除某一个断点，也可以删除全部断点。

本节教大家如何使用命令删除或者禁用之前已建好的断点，讲解过程中，将以调试如下 C 语言程序为例：

```

#include <stdio.h>
int main(){
    int num = 0;
    scanf("%d", &num);
    printf("%d", num);
    return 0;
}
```

程序存储在`~/demo/main.c`文件中，并已将其编译为可调试的 main.exe 可执行文件：

[root@bogon demo]# gcc main.c -o main.exe -g
[root@bogon demo]# ls
main.c  main.exe

## 查看当前已建好的断点

对于当前调试环境中已经建好且尚未删除的断点，可以通过以下  2 种方式查看它们。

1) 借助如下指令，可以查看当前调试环境中存在的所有断点，包括普通断点、观察断点以及捕捉断点：

(gdb) info breakpoint [n]
(gdb) info break [n]

参数 n 作为可选参数，为某个断点的编号，表示查看指定断点而非全部断点。

要知道，任何类型的断点在建立时，GDB 调试器都会为其分配一个独一无二的断点编号。以 main.exe 为例，我们尝试建立如下断点：

(gdb) b 1
Breakpoint 1 at 0x1189: file main.c, line 2.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:2
2 int main(){
(gdb) watch num
Hardware watchpoint 2: num
(gdb) catch throw int
Catchpoint 3 (throw)

可以看到，我们通过 break 命令建立了一个普通断点，其编号为 1；通过 watch 命令建立了一个观察断点，其编号为 2；通过 catch 命令建立了一个捕捉断点，其编号为 3。

在此基础上，可以通过执行 info break 或者 info breakpoint 命令，查看所有断点的具体信息：

(gdb) info break
Num     Type                      Disp Enb Address                                                             What
1           breakpoint            keep y     0x0000555555555189 in main at main.c:2       breakpoint already hit 1 time
2           hw watchpoint      keep y                                                                               num
3           catchpoint             keep y     exception throw                                                matching: int
(gdb)

借助每个断点不同的编号，也可以进行精准查询：

(gdb) info break 1
Num     Type              Disp  Enb  Address                                                                What
1           breakpoint     keep y     0x000000000040053c in main at main.c:2            breakpoint already hit 1 time

> 以上输出信息中各列的含义分别是：断点编号（Num）、断点类型（Type）、是临时断点还是永久断点（Disp）、目前是启用状态还是禁用状态（Enb）、断点的位置（Address）、断点当前的状态（作用的行号、已经命中的次数等，用 What 列表示）。

2) 除此之外，对于调试环境中已经建好且未删除的观察断点，也可以使用 info watchpoint 命令进行查看，语法格式如下：

(gdb) info watchpoint [n]

n 为可选参数，为某个观察断点的编号，功能是只查看该编号的观察断点的信息，而不是全部的观察断点。

继续在上面的调试环境中，执行如下指令：

(gdb) info watchpoint
Num     Type                  Disp Enb Address            What
2           hw watchpoint  keep y                              num
(gdb) info watchpoint 1
No watchpoint number 1.

由于当前环境中仅有 1 个观察断点，因此 info watchpoint 命令仅罗列了编号为 2 的观察断点的信息。需要注意的是，该命令仅能用于查看观察断点，普通断点和捕捉断点无法使用该命令。

## GDB 删除断点

无论是普通断点、观察断点还是捕捉断点，都可以使用 clear 或者 delete 命令进行删除。

#### 1) clear 命令

clear 命令可以删除指定位置处的所有断点，常用的语法格式如下所示：

(gdb) clear location

参数 location 通常为某一行代码的行号或者某个具体的函数名。当 location 参数为某个函数的函数名时，表示删除位于该函数入口处的所有断点。

在上面调试环境中，继续执行如下命令：

(gdb) clear 2

(gdb) info break
Deleted breakpoint 1
Num     Type                   Disp Enb Address               What
2            hw watchpoint  keep y                                 num
3            catchpoint         keep y     exception throw  matching: int
(gdb)

可以看到，断点编号为 1、位于程序第 2 行代码处的普通断点已经被删除了。

#### 2) delete 命令

delete 命令（可以缩写为 d ）通常用来删除所有断点，也可以删除指定编号的各类型断点，语法格式如下：

delete [breakpoints] [num]

其中，breakpoints 参数可有可无，num 参数为指定断点的编号，其可以是 delete 删除某一个断点，而非全部。

举个例子：

(gdb) delete 2
(gdb) info break
Num     Type              Disp Enb Address                 What
3           catchpoint     keep y    exception throw     matching: int

可以看到，delete 命令删除了编号为 2 的观察断点。

如果不指定 num 参数，则 delete 命令会删除当前程序中存在的所有断点。例如：

(gdb) delete
Delete all breakpoints? (y or n) y
(gdb) info break
No breakpoints or watchpoints.

## GDB 禁用断点

所谓禁用，就是使目标断点暂时失去作用，必要时可以再将其激活，恢复断点原有的功能。

禁用断点可以使用 disable 命令，语法格式如下：

disable [breakpoints] [num...]

breakpoints 参数可有可无；num... 表示可以有多个参数，每个参数都为要禁用断点的编号。如果指定 num...，disable 命令会禁用指定编号的断点；反之若不设定 num...，则 disable 会禁用当前程序中所有的断点。

举个例子：

(gdb) info break
Num     Type                   Disp Enb    Address                                     What
1           breakpoint         keep y        0x0000555555555189 in main  at main.c:2 breakpoint already hit 1 time
2           hw watchpoint   keep y                                                          num
3           catchpoint          keep y        exception throw                       matching: int
(gdb) disable 1 2
(gdb) info break
Num     Type                  Disp Enb    Address                                      What
1           breakpoint        keep n       0x0000555555555189 in main    at main.c:2 breakpoint already hit 1 time
2           hw watchpoint  keep n                                                           num
3           catchpoint         keep y       exception throw                          matching: int
(gdb)

可以看到，对于用 disable 命令禁用的断点，Enb 列用 n 表示其处于禁用状态，用 y 表示该断点处于激活状态。

对于禁用的断点，可以使用 enable 命令激活，该命令的语法格式有多种，分别对应有不同的功能：

enable [breakpoints] [num...]                        激活用 num... 参数指定的多个断点，如果不设定 num...，表示激活所有禁用的断点
enable [breakpoints] once num…                 临时激活以 num... 为编号的多个断点，但断点只能使用 1 次，之后会自动回到禁用状态
enable [breakpoints] count num...      临时激活以 num... 为编号的多个断点，断点可以使用 count 次，之后进入禁用状态
enable [breakpoints] delete num…               激活 num.. 为编号的多个断点，但断点只能使用 1 次，之后会被永久删除。

其中，breakpoints 参数可有可无；num... 表示可以提供多个断点的编号，enable 命令可以同时激活多个断点。

仍以上面的调试环境为例，当下程序停止在第 2 行（main() 函数开头处），此时执行如下命令：

(gdb) info break
Num     Type                  Disp Enb  Address                                       What
1           breakpoint        keep n     0x0000555555555189 in main    at main.c:2 breakpoint already hit 1 time
2           hw watchpoint  keep n                                                         num
3           catchpoint         keep y     exception throw                          matching: int
(gdb) enable delete 2
(gdb) info break
Num     Type                   Disp   Enb  Address                                       What
1           breakpoint         keep   n     0x0000555555555189 in main    at main.c:2 breakpoint already hit 1 time
2           hw watchpoint   del      y                                                          num
3           catchpoint          keep y       exception throw                           matching: int
(gdb) c
Continuing.

Hardware watchpoint 2: num

Old value = 32767
New value = 0
main () at main.c:4
4     scanf("%d", &num);
(gdb) info break
Num     Type               Disp Enb    Address                                      What
1           breakpoint     keep n        0x0000555555555189 in main   at main.c:2  breakpoint already hit 1 time
3           catchpoint     keep y         exception throw                         matching: int
(gdb)

可以看到，通过借助 enable delete 命令，我们激活了编号为 2 的观察断点，但其只能作用 1 次，因此当继续执行程序时，其会在程序第 4 行暂停，随时该断点会被删除。