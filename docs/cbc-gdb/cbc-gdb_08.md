# gdb run（r）命令：启动程序

> 原文：[`c.biancheng.net/view/8174.html`](http://c.biancheng.net/view/8174.html)

使用 GDB 调试器调试程序的过程，其实就是借助 GDB 调试器来监控程序的执行流程，进而发现程序中导致异常或者 Bug 的代码。通过前面章节的学习，读者已经学会了如何启动 GDB 调试器，在此基础上，本节继续为大家讲解如何在 GDB 调试器中启动（运行）程序，以及启动程序过程中的一些注意事项。

根据不同场景的需要，GDB 调试器提供了多种方式来启动目标程序，其中最常用的就是 run 指令，其次为 start 指令。也就是说，run 和 start 指令都可以用来在 GDB 调试器中启动程序，它们之间的区别是：

*   默认情况下，run 指令会一直执行程序，直到执行结束。如果程序中手动设置有断点，则 run 指令会执行程序至第一个断点处；
*   start 指令会执行程序至 main() 主函数的起始位置，即在 main() 函数的第一行语句处停止执行（该行代码尚未执行）。

> 可以这样理解，使用 start 指令启动程序，完全等价于先在 main() 主函数起始位置设置一个断点，然后再使用 run 指令启动程序。另外，程序执行过程中使用 run 或者 start 指令，表示的是重新启动程序。

问一个问题，GDB 调试器启动后是否就可以直接使用 run 或者 start 指令了呢？答案当然是否定的。我们知道，启动 GDB 调试器的方式有多种，其中简单的方法就是直接使用 gdb 指令，例如：

[root@bogon demo]# gdb
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
......  <-- 省略部分输出信息
Type "apropos word" to search for commands related to "word".
(gdb)

注意，使用此方式启动的 GDB 调试器，尚未指定要调试的目标程序，何谈使用 run 或者 start 指令呢？

不仅如此，在进行 run 或者 start 指令启动目标程序之前，还可能需要做一些必要的准备工作，大致包括以下几个方面：

*   如果启动 GDB 调试器时未指定要调试的目标程序，或者由于各种原因 GDB 调试器并为找到所指定的目标程序，这种情况下就需要再次手动指定；
*   有些 C 或者 C++ 程序的执行，需要接收一些参数（程序中用 argc 和 argv[] 接收）；
*   目标程序在执行过程中，可能需要临时设置 PATH 环境变量；
*   默认情况下，GDB 调试器将启动时所在的目录作为工作目录，但很多情况下，该目录并不符合要求，需要在启动程序手动为 GDB 调试器指定工作目录。
*   默认情况下，GDB 调试器启动程序后，会接收键盘临时输入的数据，并将执行结果会打印在屏幕上。但 GDB 调试器允许对执行程序的输入和输出进行重定向，使其从文件或其它终端接收输入，或者将执行结果输出到文件或其它终端。

假设使用 GDB 调试器调试如下程序：

```

//存储路径为 /tmp/demo/main.c
//其生成的可执行文件为 main.exe，位于同一路径下
#include<stdio.h>
int main(int argc,char* argv[])
{
    FILE * fp;
    if((fp = fopen(argv[1],"r")) == NULL){
        printf("file open fail");
    }
    else{
        printf("file open true");
    }
    return 0;
}
```

要知道，命令行窗口打开时默认位于 ~ （表示当前用户的主目录）路径下，假设我们就位于此目录中使用 gdb 命令启动 GDB 调试器，则在执行 main.exe 之前，有以下几项操作要做：

1) 首先，对于已启动的 GDB 调试器，我们可以先通过 l （小写的 L）指令验证其是否已找到指定的目标程序文件：

[root@bogon ~]# gdb -q     <-- 使用 -q 选项，可以省略不必要的输出信息
(gdb) l
No symbol table is loaded.  Use the "file" command.

可以看到，对于找不到目标程序文件的 GDB 调试器，l 指令的执行结果显示“无法加载符号表”。这种情况下，我们就必须手动为其指定要调试的目标程序，例如：

(gdb) file /tmp/demo/main.exe
Reading symbols from /tmp/demo/main.exe...
(gdb) l
1 #include<stdio.h>
2 int main(int argc,char* argv[])
3 {
4     FILE * fp;
5     if((fp = fopen(argv[1],"r")) == NULL){
6         printf("file open fail");
7     }
8     else{
9         printf("file open true");
10     }
(gdb)
11     return 0;
12 }
(gdb)

可以看到，通过借助 file 命令，则无需重启 GDB 调试器也能指定要调试的目标程序文件。

> 除了 file 指令外，GDB 调试器还提供有其它的指定目标调试文件的指令，感兴趣的读者可千万 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Files.html#Files)做详细了解，后续章节在用到时也会做详细的讲解。

2) 通过分析 main.c 中程序的逻辑不难发现，要想其正确执行，必须在执行程序的同时给它传递一个目标文件的文件名。

总的来说，为 GDB 调试器指定的目标程序传递参数，常用的方法有 3 种：
1、启动 GDB 调试器时，可以在指定目标调试程序的同时，使用 --args 选项指定需要传递给该程序的数据。仍以 main.exe 程序为例：

[root@bogon demo]# gdb --args main.exe a.txt

整个指令的意思是：启动 GDB 调试器调试 main.exe 程序，并为其传递 "a.txt" 这个字符串（其会被 argv[] 字符串数组接收）。

2、GDB 调试器启动后，可以借助 set args 命令指定目标调试程序启动所需要的数据。仍以 main.exe 为例：

(gdb) set args a.txt

该命令表示将 "a.txt" 传递给将要调试的目标程序。

3、除此之外，还可以使用 run 或者 start 启动目标程序时，指定其所需要的数据。例如：

(gdb) run a.txt
(gdb) start a.txt

以上 2 条命令都可以将 "a.txt" 传递给要调试的程序。

3) 要知道，对于调试 /tmp/demo/ 路径下的 main.exe 文件，将其作为 GDB 调试器的工作目录，一定程度上可以提高我们的调试效率。反之，如果 GDB 调试器的工作目录和目标调试文件不在同一目录，则很多时候需要额外指明要操作文件的存储路径（例如第 1) 种情况中用 file 指令指明调试文件时就必须指明其存储位置）。

默认情况下，GDB 调试器的工作目录为启动时所使用的目录。例如在 ~ 路径下启动的 GDB 调试器，其工作目录就为 ~（当前用户的 home 目录）。当然，GDB 调试器提供有修改工作目录的指令，即 cd 指令。例如，将 GDB 调试器的工作目录修改为 /tmp/demo，则执行指令为：

(gdb) cd /tmp/demo

由此，GDB 调试器的工作目录就变成了 /tmp/demo。

4) 某些场景中，目标调试程序的执行还需要临时修改 PATH 环境变量，此时就可以借助 path 指令，例如：

(gdb) path /temp/demo
Executable and object file path: /temp/demo:/usr/local/sbin:/usr/local/bin...

> 注意，此修改方式只是临时的，退出 GDB 调试后会失效。

5) 默认情况下，GDB 调试的程序会接收 set args 等方式指定的参数，同时会将输出结果打印到屏幕上。而通过对输入、输出重定向，可以令调试程序接收指定文件或者终端提供的数据，也可以将执行结果输出到文件或者某个终端上。

例如，将 main.exe 文件的执行结果输出到 a.txt 文件中，执行如下命令：

(gdb) run > a.txt

由此，在 GDB 调试的工作目录下就会生成一个 a.txt 文件，其中存储的即为 main.exe 的执行结果。

总的来说，只有将调试程序所需的运行环境搭建好后，才能使用 run 或者 start 命令开始调试。如下是一个完整的实例，演示了 GDB 调试 mian.exe 之前所做的准备工作：

[root@bogon demo]# pwd   <--显示当前工作路径
/tmp/demo
[root@bogon demo]# ls       <-- 显示当前路径下的文件
a.txt  main.c  main.exe
[root@bogon demo]# cd ~  <-- 进入 home 目录
[root@bogon ~]# gdb -q      <-- 开启 GDB 调试器
(gdb) cd /tmp/demo            <-- 修改 GDB 调试器的工作目录
Working directory /tmp/demo.
(gdb) file main.exe               <-- 指定要调试的目标文件
Reading symbols from main.exe...
(gdb) set args a.txt               <-- 指定传递的数据
(gdb) run                               <-- 运行程序
Starting program: /tmp/demo/main.exe a.txt
file open true[Inferior 1 (process 43065) exited normally]