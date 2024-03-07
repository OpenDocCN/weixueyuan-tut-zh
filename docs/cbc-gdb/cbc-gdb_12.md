# GDB 条件断点（condition 命令）详解

> 原文：[`c.biancheng.net/view/8255.html`](http://c.biancheng.net/view/8255.html)

前面章节给大家介绍了 GDB 调试器中普通断点、观察断点以及捕捉断点的功能和用法。其中值得一提的是，对于普通断点的建立，可以使用如下格式的 break 命令：

(gdb) break ... if cond

... 参数用于指定生成断点的具体位置；cond 参数用于代指某个表达式。通过此方式建立的普通断点，只有当表达式 cond 成立（值为 True）时，才会发挥它的作用；反之，断点并不会使程序停止执行。

类似上面这种，以某个表达式的是否成立作为条件，从而决定自身是否生效的断点，又称为条件断点。除了普通断点外，观察断点和捕捉断点也可以成为条件断点。

需要说明的是，创建普通条件断点的方式，也同样适用于观察条件断点。通过执行如下命令，即可直接生成一个观察条件断点：

(gdb) watch expr if cond

参数 expr 表示要观察的变量或表达式；参数 cond 用于代指某个表达式。

但是，以上创建条件断点的方法，不适用于捕捉断点。换句话说，捕捉条件断点无法直接生成，需要借助 condition 命令为现有捕捉断点增添一个 cond 表达式，才能使其变成条件断点。

总的来说，借助 condition 命令，我们可以将现有的普通断点、观察断点以及捕捉断点变成条件断点；而普通条件断点和观察条件断点，可以分别通过 break if 命令和 watch if 命令直接生成。

接下来，我将给大家详细地讲解 condition 命令的用法。

## GDB condition 命令

严格来说，condition 命令的功能是：既可以为现有的普通断点、观察断点以及捕捉断点添加条件表达式，也可以对条件断点的条件表达式进行修改。

condition 命令没有缩写形式，使用方法很简单，语法格式如下：

(gdb) condition bnum expression
(gdb) condition bnum

参数 bnum 用于代指目标断点的编号；参数 expression 表示为断点添加或修改的条件表达式。

以上 2 种语法格式中，第 1 种用于为 bnum 编号的断点添加或修改 expression 条件表达式；第 2 种用于删除 bnum 编号断点的条件表达式，使其变成普通的无条件断点。

举个例子，这里以调试如下 C++ 程序为例：

```

#include <iostream>
using namespace std;
int main(){
    int num = 1;
    while(num<20){
        try{
            throw num;
        }catch(int &e){
            num++;
        }
    }
    cout << num << endl;
    return 0;
}
```

程序存储位置为`~/demo/main.cpp`，并已经生成了可供 GDB 调试器使用的执行文件：

[root@bogon demo]# ls
main.cpp  main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from ~/demo/main.exe...done.
(gdb) l
1 #include <iostream>
2 using namespace std;
3 int main(){
4     int num = 1;
5     while(num<20){
6         try{
7             throw num;
8         }catch(int &e){
9             num++;
10         }
(gdb)
11     }
12     cout << num << endl;
13     return 0;
14 }
(gdb)

接下来，为读者演示 condition 命令的功能和用法：

(gdb) b 9     <--添加普通断点
Breakpoint 1 at 0x12d0: file main.cpp, line 9.
(gdb) r
Starting program: /home/test/demo/main.exe

Breakpoint 1, main () at main.cpp:9
9             num++;
(gdb) rwatch num                 <-- 添加观察断点
Hardware read watchpoint 2: num
(gdb) catch throw int            <-- 添加捕捉断点
Catchpoint 3 (throw)
(gdb) info break
Num     Type            Disp Enb Address                        What
1       breakpoint      keep y     0x00005555555552d0  in main() at main.cpp:9    breakpoint already hit 1 time
2       read watchpoint keep y                                       num
3       catchpoint      keep y                                           exception throw      matching: int
(gdb) condition 1 num==3             <-- 为普通断点添加条件表达式
(gdb) condition 2 num==5             <-- 为观察断点添加条件表达式
(gdb) condition 3 num==7             <-- 为捕捉断点添加条件表达式
(gdb) c
Continuing.

Breakpoint 1, main () at main.cpp:9            <-- 普通条件断点触发
9             num++;
(gdb) p num
$1 = 3
(gdb) c
Continuing.

Hardware read watchpoint 2: num             <-- 观察条件断点触发

Value = 5
0x000055555555526f in main () at main.cpp:7
7             throw num;
(gdb) c
Continuing.

Catchpoint 3 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()       <-- 捕捉条件断点触发
   from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1  0x0000555555555285 in main () at main.cpp:7
7             throw num;
(gdb) p num
$2 = 7
(gdb)

可以看到，通过借助 condition 命令为不同类型断点设置条件表达式，只有当条件表达式成立（值为 True）时，相应的断点才会触发从而使程序暂停运行。

#### GDB ignore 命令

ignore 命令也可以使一个断点成为条件断点，但这里的“条件”并非自定义的表达式，而仅为一个整数，它用来表示该断点失效的次数。也就会说，ignore 命令可以使目标断点暂时失去作用，当断点失效的次数超过指定次数时，断点的功能会自动恢复。

ignore 命令也没有缩写形式，其语法格式如下：

ignore bnum count

参数 bnum 为某个断点的编号；参数 count 用于指定该断点失效的次数。

仍以 main.exe 为例：

(gdb) b 9
Breakpoint 1 at 0x400a33: file main.cpp, line 9.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.cpp:9
9             num++;
(gdb) p num
$1 = 1
(gdb) ignore 1 3
Will ignore next 3 crossings of breakpoint 1.
(gdb) c
Continuing.

Breakpoint 1, main () at main.cpp:9
9             num++;
(gdb) p num
$2 = 5
(gdb)

可以看到，执行 ignore 命令之前，num 变量的值为 1。借助 ignore 命令使编号为 1（作用于第 9 行）的断点失效 3 次后，继续执行程序，最终程序仍暂停至第 9 行，此时 num 的值变为 5。这这恰恰证明了 num 从 1 递增至 5 的过程中，编号为 1 的断点失效了 3 次。