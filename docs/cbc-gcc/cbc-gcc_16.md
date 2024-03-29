# GCC 编译和链接多个文件（包括源文件、目标文件、汇编文件等）

编译多个源代码文件会生成多个目标文件，每个目标文件都包含一个源文件的机器码和相关数据的符号表。除非使用`-c`选项指示 GCC 只编译不链接，否则 GCC 会使用临时文件作为目标文件输出：

$ gcc -c main.c
$ gcc -c func.c

这些命令会在当前目录中生成两个目标文件，分别是 main.o 和 func.o。把两个源文件名放在同一个 GCC 命令中，也可以获得同样的结果：

$ gcc -c main.c func.c

然而，实际上编译器通常每次只会被调用来完成一件小型任务。大的程序包含许多源文件，在开发期间必须被编译、测试、编辑，然后再编译，很少会有在创建中的修改行为会影响所有的源文件。为了节省时间，可以使用 make 控制创建过程，由它调用编译器重新编译，而且只编译比对应的最新源文件旧的那些目标文件。

一旦所有当前源文件都被编译为目标文件，就可以使用 GCC 来链接它们：

$ gcc main.o func.o -o app.out -lm

GCC 假设扩展名为`.o`的文件是要被链接的目标文件。

## 文件类型

编译器支持许多和 C 语言程序相关的扩展名，对它们的说明如下：

| 扩展名（后缀） | 说明 |
| .c | C 程序源代码，在编译之前要先进行预处理。 |
| .i | C 程序预处理输出，可以被编译。 |
| .h | C 程序头文件。（为了节省时间，许多源文件会包含相同的头文件，GCC 允许事先编译好头文件，称为“预编译头文件”，它合适情况下自动被用于编译。） |
| .s | 汇编语言。 |
| .S | 有 C 命令的汇编语言，在汇编之前必须先进行预处理。 |

GCC 也支持以下扩展名：.ii、.cc、.cp、.cxx、.cpp、.CPP、.c++、.C、.hh、.H、.m、.mi、.f、.for、.FOR、.F、.fpp、.FPP、.r、.ads 和 .adb。这些文件类型涉及 C++、Objective-C、Fortran 或 Ada 等程序的编译。使用其他扩展名的文件会被视为对象文件，以作为链接之用。

如果使用其他命名惯例为输入文件命名，可以使用`-x file_type`选项来指示 GCC 应该如何对待这些文件。file_type 必须是下面其中之一：c、c-header、cpp-output、assembler（表示该文件包含汇编语言）、assembler-with-cpp 或 none。

在命令行中，`-x`后面所列的所有文件都会被视为所指定的类型。如果想改变类型，可以再次使用`-x`。如下例所示：

$ gcc -o bigprg mainpart.c -x assembler trickypart.asm -x c otherpart.c

可以在同一个命令行中多次使用`-x`选项，以指示不同类型的文件。`-x none`选项会全部取消这些指示，后续文件会按照 GCC 默认规则解释它们的扩展名。

## 混合输入类型

可以在 GCC 命令行中混合使用其他输入文件类型。如果编译器不能按照请求处理指定的文件，那么它会忽略它们。如下例所示：

$ gcc main.c func.o -o app.out -lm

借助于这个命令，假设指定的文件都存在，GCC 会编译和汇编 main.c，汇编 func.o，并且链接库文件 libm.a。