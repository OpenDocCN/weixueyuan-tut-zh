# GDB 调试教程：1 小时玩转 Linux gdb 命令

> 原文：[`c.biancheng.net/gdb/`](http://c.biancheng.net/gdb/)

![GDB 调试器](img/3daef1cd1ebb81e03de0830774e80976.png)GNU symbolic debugger，简称「**GDB 调试器**」，是 Linux 平台下最常用的一款程序调试器。GDB 编译器通常以 gdb 命令的形式在终端（Shell）中使用，它有很多选项，这是我们要重点学习的。

发展至今，GDB 调试器已经对 C、C++、Go、Objective-C、OpenCL、Ada 等多种编程语言提供了支持。实际场景中，GDB 更常用来调试 C 和 C++ 程序，虽然 Linux 平台下有很多能编写 C、C++ 代码的集成开发工具（IDE），但它们调试代码的能力往往都源自 GDB 调试器。

调试是开发流程中一个非常重要的环境，每个程序员都应具备调试代码的能力，尤其对于从事 Linux C/C++ 开发的读者，必须具备熟练使用 GDB 调试器的能力。这套 GDB 入门教程通俗易懂，深入浅出，能让你快速学会使用 GDB 编译器。

> 阅读本教程需要读者具备 C/C++ 基础，如果你还不了解 C/C++，请转到《C 语言入门教程》《C++入门教程》。