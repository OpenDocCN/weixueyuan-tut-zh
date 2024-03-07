# Makefile 路径搜索使用案例

> 原文：[`c.biancheng.net/view/7153.html`](http://c.biancheng.net/view/7153.html)

我们了解了一下路径搜索的使用方式，我们再来看一下具体的使用方法。

为了体验实例的效果的更加明显，我们按照源代码树的布局来放置文件。我们把源代码放置在 src 目录下，包含的文件文件是：list1.c、list2.c、main.c 文件，我们把头文件包含在 include 的目录下，包含文件 list1.h、list2.h 文件。Makefile 放在这两个目录文件的上一级目录。

我们按照之前的方式来编写 Makefile 文件：

```

main:main.o list1.o list2.o
    gcc -o $@ $<
main.o:main.c
    gcc -o $@ $^
list1.o:list1.c list1.h
    gcc -o $@ $<
list2.o:list2.c list2.h
    gcc -o $@ $<

```

我们编译执行的 make 时候会发现命令行提示我们：

make:*** No rule to make target 'main.c',need by 'main.o'. stop.

出现错误并且编译停止了，为什么会出现错误呢？我们来看一下出现错误的原因，再去重建最终目标文件 main 的时候我们需要 main.o 文件，但是我们再去重建目标 main.o 文件的时候，发现没有找到指定的 main.c 文件，这是错误的根本原因。

这个时候我们就应该添加上路径搜索，我们知道路径搜索的方法有两个：VPATH 和 vpath。我们先来使用一下 VPATH，使用方式很简单，我们只需要在上述的文件开头加上这样一句话：

VPATH=src include

再去执行 make 就不会出现错误。所以 Makefile 中的最终写法是这样的：

```

VPATH=src include
main:main.o list1.o list2.o
    gcc -o $@ $<
main.o:main.c
    gcc -o $@ $^
list1.o:list1.c list1.h
    gcc -o $@ $<
list2.o:list2.c list2.h
    gcc -o $@ $<

```

我们使用 vpath 的话同样可以解决这样的问题，只需要把上述代码中的 VPATH 所在行的代码改写成：

vpath %.c src
vpath %.h include

这样我们就可以用 vpath 实现功能，代码的最终展示为：

```

vpath %.c src
vpath %.h include
main:main.o list1.o list2.o
    gcc -o $@ $<
main.o:main.c
    gcc -o $@ $^
list1.o:list1.c list1.h
    gcc -o $@ $<
list2.o:list2.c list2.h
    gcc -o $@ $<

```