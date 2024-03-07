# Makefile 教程：Makefile 文件编写 1 天入门

> 原文：[`c.biancheng.net/makefile/`](http://c.biancheng.net/makefile/)

![Makefile 文件图标](img/7e5a05ad9235657c646732c611f1b2fa.png)Makefile 文件描述了 Linux 系统下 C/C++ 工程的编译规则，它用来自动化编译 C/C++ 项目。一旦写编写好 Makefile 文件，只需要一个 make 命令，整个工程就开始自动编译，不再需要手动执行 GCC 命令。

一个中大型 C/C++ 工程的源文件有成百上千个，它们按照功能、模块、类型分别放在不同的目录中，Makefile 文件定义了一系列规则，指明了源文件的编译顺序、依赖关系、是否需要重新编译等。

这套 Makefile 入门教程针对有 C/C++ 基础的读者，如果你还不了解 C/C++，请转到《C 语言教程》《C++教程》进行学习。Makefile 文件可以很复杂，它的语法甚至不比 C 语言简单；本教程化繁为简，以口语化和通俗化的手法来讲解每个知识点，同时附带大量实例，让读者学以致用。

一个 Linux C/C++ 程序员必须会编写 Makefile 文件，否则就玩不转中大型项目。