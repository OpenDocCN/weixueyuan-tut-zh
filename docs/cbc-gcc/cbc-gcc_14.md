# GCC -S 选项：生成汇编文件

编译器的核心任务是把 C 程序翻译成机器的汇编语言（assembly language）。汇编语言是人类可以阅读的编程语言，也是相当接近实际机器码的语言。由此导致每种 CPU 架构都有不同的汇编语言。

> 实际上，GCC 是一个适合多种 CPU 架构的编译器，不会把 C 程序语句直接翻译成目标机器的汇编语言，而是在输入语言和输出汇编语言之间，利用一个中间语言，称为 RegisterTransfer Language（简称 RTL，寄存器传输语言）。借助于这个抽象层，在任何背景下，编译器可以选择最经济的方式对给定的操作编码。
> 
> 而且，在交互文件中针对目标机器的抽象描述，为编译器重新定向到新架构提供了一个结构化的方式。但是，从 GCC 用户角度来看，我们可以忽略这个中间步骤。

通常情况下，GCC 把汇编语言输出存储到临时文件中，并且在汇编器执行完后立刻删除它们。但是可以使用`-S`选项，让编译程序在生成汇编语言输出之后立刻停止。

如果没有指定输出文件名，那么采用`-S`选项的 GCC 编译过程会为每个被编译的输入文件生成以`.s`作为后缀的汇编语言文件。如下例所示：

$ gcc -S circle.c

编译器预处理 circle.c，将其翻译成汇编语言，并将结果存储在 circle.s 文件中。

如果想把 C 语言变量的名称作为汇编语言语句中的注释，可以加上`-fverbose-asm`选项：

$ gcc -S -fverbose-asm circle.c