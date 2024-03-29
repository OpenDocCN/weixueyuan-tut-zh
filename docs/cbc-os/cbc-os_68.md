# 关于操作系统运行环境的习题

提示：以下习题涉及教程 1.7 节及其前面的内容，请至少阅读完 1.7 节后再浏览习题。

## 一、单项选择题

1.下列关于操作系统的说法中，错误的是（）。

Ⅰ.在通用操作系统管理下的计算机上运行程序，需要向操作系统预定运行时间

Ⅱ.在通用操作系统管理下的计算机上运行程序，需要确定起始地址，并从这个地址开始执行

Ⅲ.操作系统需要提供髙级程序设计语言的编译器

Ⅳ.管理计算机系统资源是操作系统关心的主要问题

A. Ⅰ、Ⅲ    B. Ⅱ、Ⅲ    C. Ⅰ、Ⅱ、Ⅲ、Ⅳ    D.以上答案都正确

2.下列说法正确的是（ ）。

Ⅰ.批处理的主要缺点是需要大量内存

Ⅱ.当计算机提供了核心态和用户态时，输入/输出指令必须在核心态下执行

Ⅲ.操作系统中釆用多道程序设计技术的最主要原因是为了提髙 cpu 和外部设备的可靠性    '

Ⅳ.操作系统中，通道技术是一种硬件技术

A. Ⅰ、Ⅱ    B. Ⅰ、Ⅲ    C. Ⅱ、Ⅳ    D. Ⅱ、Ⅲ、Ⅳ

3.下列关于系统调用的说法正确的是（ ）。

Ⅰ.用户程序设计时，使用系统调用命令，该命令经过编译后，形成若干参数和陷入（trap)指令

Ⅱ.用户程序设计时，使用系统调用命令，该命令经过编译后，形成若干参数和屏蔽中断指令

Ⅲ.系统调用功能是操作系统向用户程序提供的接口

Ⅳ.用户及其应用程序和应用系统是通过系统调用提供的支持和服务来使用系统资源完成其操作的

A. Ⅰ、Ⅲ    B. Ⅱ、Ⅳ    C. Ⅰ、Ⅲ、Ⅳ    D. Ⅱ、Ⅲ、Ⅳ

4.（）是操作系统必须提供的功能。

A.图形用户界面（GUI)    B.为进程提供系统调用命令

C.中断处理    D.编译源程序

5.用户程序在用户态下要使用特权指令引起的中断属于（ ）。

A.硬件故障中断    B.程序中断    C.外部中断    D.访管中断

6.处理器执行的指令被分为两类，其中有一类称为特权指令，它只允许（ ）使用。

A.操作员    B.联机用户    C.目标程序    D.操作系统

7.下列操作系统的各个功能组成部分中，（ ）可不需要硬件的支持。

A.进程调度    B.时钟管理    C.地址映射    D.中断系统

8.在中断发生后，进入中断处理的程序属于（ ）。

A.用户程序

B.可能是应用程序，也可能是操作系统程序

C.操作系统程序

D.既不是应用程序，也不是操作系统程序

9.当计算机区分了核心态和用户态指令之后，从核心态到用户态的转换是由操作系统程序执行后完成的，而用户态到核心态的转换则是由（ ）完成的。

A.硬件    B.核心态程序    C.用户程序    D.中断处理程序

10.【2011 年计算机联考真题】

下列选项中，在用户态执行的是（ ）。

A.命令解释程序    B.缺页处理程序

C.进程调度程序    D.时钟中断处理程序

11.【2012 年计算机联考真题】

下列选项中，不可能在用户态发生的事件是（ ）。

A.系统调用    B.外部中断    C.进程切换    D.缺页

12.只能在核心态下运行的指令是（ >。

A.读时钟指令    B.置时钟指令    C.取数指令    D.寄存器清零

13.“访管”指令（ ）使用。

A.仅在用户态下    B.仅在核心态下

C.在规定时间内    D.在调度时间内

14.当 CPU 执行操作系统代码时，处理器处于（ ）。

A.自由态    B.用户态    C.核心态    D.就绪态

15.在操作系统中，只能在核心态下执行的指令是（ ）。

A.读时钟    B.取数    C.广义指令    D.寄存器清“0”

16.下列选项中，必须在核心态下执行的指令是（ ）。

A.从内存中取数    B.将运算结果装入内存

C.算术运算    D.输入/输出

17.当 CPU 处于核心态时，它可以执行的指令是（ ）。

A.只有特权指令    B.只有非特权指令

C.只有“访管”指令    D.除访管指令的全部指令

18.【2012 年计算机联考真题】

中断处理和子程序调用都需要压栈以保护现场，中断处理一定会保存而子程序调用不需要保存其内容的是（ ）。

A.程序计数器    B.程序状态字寄存器

C.通用数据寄存器    D.通用地址寄存器

## 二、综合应用题

1.处理器为什么要区分核心态和用户态两种操作方式？在什么情况下进行两种方式的切换？

2.为什么说直到出现中断和通道技术后，多道程序概念才变为有用的？

各题答案及解析请查看：[`club.coderbbs.com/thread-100-1-1.html`](http://club.coderbbs.com/thread-100-1-1.html)