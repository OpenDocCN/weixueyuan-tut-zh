# GDB print 命令高级用法

> 原文：[`c.biancheng.net/view/8252.html`](http://c.biancheng.net/view/8252.html)

《GDB print 和 display 命令》一节中，就如何在 GDB 调试代码过程中查看某一变量或表达式的值，对 print 和 display 命令的功能和基本用法做了详细的介绍。

除此之外，print 命令还有更高级的功能和用法，例如以指定的格式输出变量或者表达式的值、输出数组中指定区间内的所有元素等等，接下来将给读者一一做详细的讲解。
和 print 命令最基本的用法相比，该命令的完整语法格式如下所示：

(gdb) print [options --] [/fmt] expr

> 格式中用 [ ] 括起来的部分是可选的，可以使用也可以省略。

其中，各个参数的含义如下：

1.  options：表示该命令所支持的选项，这些选项可以控制 print 命令输出指定内容的变量或者表达式的值；
2.  fmt：指定输出变量或表达式值时所采用的格式；
3.  expr：指定要查看的变量或表达式。

1) 首先介绍 options 参数的用法，表 1 罗列了常用的几个 options 参数值。

表 1 print options 参数的取值

| options 参数 | 功 能 |
| -address on&#124;off | 查看某一指针变量的值时，是否同时打印其占用的内存地址，默认值为 on。该选项等同于单独执行 set print address on&#124;off 命令。 |
| -array on&#124;off | 是否以便于阅读的格式输出数组中的元素，默认值为 off。该选项等同于单独执行 set printf array on&#124;off 命令。 |
| -array-indexes on&#124;off | 对于非字符类型数组，在打印数组中每个元素值的同时，是否同时显示每个元素对应的数组下标，默认值为 off。该选项等同于单独执行 set print array-indexes on&#124;off 命令。 |
| -pretty on&#124;off | 以便于阅读的格式打印某个结构体变量的值，默认值为 off。该选项等同于单独执行 set print pretty on&#124;off 命令。 |

> 注意，options 参数和 /fmt 或者 expr 之间，必须用`--`（ 2 个 - 字符）分隔。此外，options 参数还有很多选项可以使用，感兴趣的读者可自行前往 [GDB 官网](https://sourceware.org/gdb/current/onlinedocs/gdb/Data.html#Data)查看，这里不再一一罗列。

2) 和 display 命令一样，print 命令可允许自定义输出格式，表 2 罗列了几个常用的 /fmt 参数。

表 2 /fmt 常用的值

| /fmt | 功 能 |
| /x | 以十六进制的形式打印出整数。 |
| /d | 以有符号、十进制的形式打印出整数。 |
| /u | 以无符号、十进制的形式打印出整数。 |
| /o | 以八进制的形式打印出整数。 |
| /t | 以二进制的形式打印出整数。 |
| /f | 以浮点数的形式打印变量或表达式的值。 |
| /c | 以字符形式打印变量或表达式的值。 |

> 当 print 命令不指定任何 options 参数时，print 和 /fmt 之间不用添加空格，例如以十六进制的形式输出 num 整形变量的值，执行命令为 (gdb) print/x num。

3) 我们知道，print 命令可以打印指定变量或表达式的值。值得一提的是，当指定目标表达式时，除了表达式本身外，GDB 调试器还支持使用`@`和`::`运算符。

`@`运算符用于输出数组中指定区域的元素，使用格式如下：

(gdb) print first@len

其中，参数 first 用于指定数组查看区域内的首个元素的值；参数 len 用于指令自 first 元素开始查看的元素个数。

假设有一个 array 数组，其定义如下：

```

int array[5] = {1,2,3,4};
```

如果我们想查看第 1 个元素和第 2 个元素的值，可以执行如下指令：

(gdb) print array[0]@2
$1 = {1, 2}

当程序中包含多个作用域不同但名称相同的变量或表达式时，可以借助`::`运算符明确指定要查看的目标变量或表达式。`::`运算符的语法格式如下：

(gdb) print file::variable
(gdb) print function::variable

其中 file 用于指定具体的文件名，funciton 用于指定具体所在函数的函数名，variable 表示要查看的目标变量或表达式。

举个例子：

```

#include <stdio.h>
int num = 10;
int main(){
    int num = 20;
    return 0;
}
```

假设该程序存储在 main.c 文件中，则使用 GDB 调试至第 5 行（return 0）处暂停后，通过执行如下命令，即可查看 num 全局变量的值：

(gdb) print 'main.c'::num
$1 = 10

而通过执行如下命令，可以查看 num 局部变量的值：

(gdb) p main::num
$1 = 20

当然，由于 GDB 调试就暂停在 main() 函数中，因此即便不指明`main::`，这里的 num 默认指代的也是 num 局部变量。

为了让读者彻底搞清楚 print 命令的用法，这里以一段 C 语言程序为例，为大家演示以上所讲 print 命令的用法：

(gdb) l
1 #include <stdio.h>
2 typedef struct website{
3     char *url;
4     int time;
5 }web;
6 int num = 10;
7 int main(){
8     int num = 20;
9     int array[10]={1,2,3,4,5,6};
10     web LanguageC = {"http://c.biancheng.net",6};
(gdb)
11     return 0;
12 }
(gdb) b 11
Breakpoint 1 at 0x11cf: file main.c, line 11.
(gdb) r
Starting program: ~/demo/main.exe

Breakpoint 1, main () at main.c:11
11     return 0;
(gdb) print -address on -- LanguageC.url                      <-- 打印 url 指针的同时，输出其所在的内存地址
$1 = 0x555555556004 "http://c.biancheng.net"
(gdb) print -address off -- LanguageC.url                      <-- 打印 url 指针，但不输出其所在的内存地址
$2 = "http://c.biancheng.net"
(gdb) print -pretty on -- LanguageC                               <-- 以便于阅读的方式，输出结构体的值
$3 = {
  url = 0x555555556004 "http://c.biancheng.net",
  time = 6
}
(gdb) print LanguageC                                                    <-- 以压缩的格式输出结构体的值
$4 = {url = 0x555555556004 "http://c.biancheng.net", time = 6}
(gdb) print/x num                                                            <-- 以十六进制的形式输出局部变量 num 的值
$5 = 0x14
(gdb) print array[1]@2                                                     <-- 从 array[1] 处开始，输出 array 数组中后续的 2 个元素的值
$6 = {2, 3}
(gdb) print num                                                                <-- 输出局部变量 num 的值
$7 = 20
(gdb) print 'main.c'::num                                                  <-- 输出全局变量 num 的值
$8 = 10
(gdb)