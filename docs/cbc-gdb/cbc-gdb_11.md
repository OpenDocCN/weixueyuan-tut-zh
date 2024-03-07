# GDB catch 命令：建立捕捉断点

> 原文：[`c.biancheng.net/view/8199.html`](http://c.biancheng.net/view/8199.html)

要知道，GDB 调试器支持在被调试程序中打 3 种断点，分别为普通断点、观察断点和捕捉断点，其中普通断点用 break 命令建立（可阅读《GDB break》一节），观察断点用 watch 命令建立（可阅读《GDB watch》一节），本节将讲解如何使用 catch 命令建立捕捉断点。

和前 2 种断点不同，普通断点作用于程序中的某一行，当程序运行至此行时停止执行，观察断点作用于某一变量或表达式，当该变量（表达式）的值发生改变时，程序暂停。而捕捉断点的作用是，监控程序中某一事件的发生，例如程序发生某种异常时、某一动态库被加载时等等，一旦目标时间发生，则程序停止执行。

> 用捕捉断点监控某一事件的发生，等同于在程序中该事件发生的位置打普通断点。

建立捕捉断点的方式很简单，就是使用 catch 命令，其基本格式为：

(gdb) catch event

其中，event 参数表示要监控的具体事件。对于使用 GDB 调试 C、C++ 程序，常用的 event 事件类型如表 1 所示。

表 1 常见的 event 事件

| event 事件 | 含 义 |
| throw [exception] | 当程序中抛出 exception 指定类型异常时，程序停止执行。如果不指定异常类型（即省略 exception），则表示只要程序发生异常，程序就停止执行。 |
| catch [exception] | 当程序中捕获到 exception 异常时，程序停止执行。exception 参数也可以省略，表示无论程序中捕获到哪种异常，程序都暂停执行。 |
| load [regexp] unload [regexp] | 其中，regexp 表示目标动态库的名称，load 命令表示当 regexp 动态库加载时程序停止执行；unload 命令表示当 regexp 动态库被卸载时，程序暂停执行。regexp 参数也可以省略，此时只要程序中某一动态库被加载或卸载，程序就会暂停执行。 |

> 除表中罗列的以外，event 参数还有其它一些写法，感兴趣的读者可查看 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Set-Catchpoints.html#Set-Catchpoints)进行了解，这里不再过多赘述。

注意，当前 GDB 调试器对监控 C++ 程序中异常的支持还有待完善，使用 catch 命令时，有以下几点需要说明：

1.  对于使用 catch 监控指定的 event 事件，其匹配过程需要借助 libstdc++ 库中的一些 SDT 探针，而这些探针最早出现在 GCC 4.8 版本中。也就是说，想使用 catch 监控指定类型的 event 事件，系统中 GCC 编译器的版本最低为 4.8，但即便如此，catch 命令是否能正常发挥作用，还可能受到系统中其它因素的影响。
2.  当 catch 命令捕获到指定的 event 事件时，程序暂停执行的位置往往位于某个系统库（例如 libstdc++）中。这种情况下，通过执行 up 命令，即可返回发生 event 事件的源代码处。
3.  catch 无法捕获以交互方式引发的异常。

如同 break 命令和 tbreak 命令的关系一样（前者的断点是永久的，后者是一次性的），catch 命令也有另一个版本，即 tcatch 命令。tcatch 命令和 catch 命令的用法完全相同，唯一不同之处在于，对于目标事件，catch 命令的监控是永久的，而 tcatch 命令只监控一次，也就是说，只有目标时间第一次触发时，tcath 命令才会捕获并使程序暂停，之后将失效。

接下来就以下面的 C++ 程序为例，给大家演示 catch（tcatch）命令的用法：

```

#include <iostream>
using namespace std;

int main(){
    int num = 1;
    while(num <= 5){
        try{
            throw 100;
        }catch(int e){
            num++;
            cout << "next" << endl;
        }
    }
    cout << "over" << endl;
    return 0;
}
```

此程序存储于 ~/demo/main.cpp 文件中（ ~ 表示当前登陆用户的主目录）。

在此基础上，对 main.cpp 文件进行编译，并启动对该程序的调试：

[root@bogon demo]$ ls
main.cpp
[root@bogon demo]# g++ main.cpp -o main.exe -g
[root@bogon demo]$ ls
main.cpp  main.exe
[root@bogon demo]# gdb main.exe -q
Reading symbols from main.exe...done.
(gdb)

通过观察程序可以看出，当前程序中通过 throw 手动抛出了 int 异常，此异常能够被 catch 成功捕获。假设我们使用 catch 命令监控：只要程序中引发 int 异常，程序就停止执行：

(gdb) catch throw int              <-- 指定捕获“throw int”事件
Catchpoint 1 (throw)
(gdb) r                                     <-- 执行程序
Starting program: ~/demo/main.exe

Catchpoint 1 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()
   from /lib/x86_64-linux-gnu/libstdc++.so.6                          <-- 程序暂停执行
(gdb) up                                                                                    <-- 回到源码
#1  0x0000555555555287 in main () at main.cpp:8
8             throw 100;
(gdb) c                                                                                      <-- 继续执行程序
Continuing.
next

Catchpoint 1 (exception thrown), 0x00007ffff7e81762 in __cxa_throw ()
   from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1  0x0000555555555287 in main () at main.cpp:8
8             throw 100;
(gdb) 

如上所示，借助 catch 命令设置了一个捕获断点，该断点用于监控 throw int 事件，只要发生程序就会暂停执行。由此当程序执行时，其会暂停至 libstdc++ 库中的某个位置，借助 up 指令我们可以得知该异常发生在源代码文件中的位置。

同理，我们也可以监控 main.cpp 程序中发生的  catch event 事件：

(gdb) catch catch int
Catchpoint 1 (catch)
(gdb) r
Starting program: ~/demo/main.exe

Catchpoint 1 (exception caught), 0x00007ffff7e804d3 in __cxa_begin_catch ()
   from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1  0x00005555555552d0 in main () at main.cpp:9
9         }catch(int e){
(gdb) c
Continuing.
next

Catchpoint 1 (exception caught), 0x00007ffff7e804d3 in __cxa_begin_catch ()
   from /lib/x86_64-linux-gnu/libstdc++.so.6
(gdb) up
#1  0x00005555555552d0 in main () at main.cpp:9
9         }catch(int e){
(gdb)

甚至于，在个别场景中，还可以使用 catch 命令监控 C、C++ 程序动态库的加载和卸载。就以 main.exe 为例，其运行所需加载的动态库可以使用 ldd 命令查看，例如：

[root@bogon demo]# ldd main.exe
linux-vdso.so.1 =>  (0x00007fffbc1ff000)
libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x0000003e75000000)
libm.so.6 => /lib64/libm.so.6 (0x00000037eee00000)
libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000003e74c00000)
libc.so.6 => /lib64/libc.so.6 (0x00000037ee200000)
/lib64/ld-linux-x86-64.so.2 (0x00000037eda00000)

就以监控 libstdc++.so.6 为例，在 GDB 调试器中，通过执行如下指令，即可监控该动态库的加载：

(gdb) catch load libstdc++.so.6
Catchpoint 1 (load)
(gdb) r
Starting program: ~/demo/main.exe

Catchpoint 1
  Inferior loaded /lib/x86_64-linux-gnu/libstdc++.so.6
    /lib/x86_64-linux-gnu/libgcc_s.so.1
    /lib/x86_64-linux-gnu/libc.so.6
    /lib/x86_64-linux-gnu/libm.so.6
0x00007ffff7fd37a5 in ?? () from /lib64/ld-linux-x86-64.so.2

> 以上实例仅演示了 catch 命令的用法，而 tcatch 命令的语法格式和 catch 完全相同，读者可自行尝试使用 tcatch 命令，观察它的功能。