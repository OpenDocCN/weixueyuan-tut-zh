# GNU 编译器套件

GCC 原来代表“GNU C Compiler”的意思。自从面世后，GCC 逐渐扩充、发展，现在不仅仅支持 C 语言，还支持其他很多语言，包括 C++、Ada、Objective-C、Fortran 和 Java 等。因此，GCC 的意思被重新定义为“GNU Compiler Collection”，也即“GUN 编译器套件”。

GUN 编译器套件包含多种前端处理器，以翻译各种不同语言。当然，在本教程中我们重点讨论的是基于 C 语言的前端处理器 GCC。

GCC 也是一种多目标（multitarget）编译器；换句话说，它通过使用可互换的后端处理器，为多种不同的计算机架构生成相应的可执行程序。

正如模块化概念所提倡的，GCC 可被用作交互式编译器；也就是说，可以使用 GCC 对所有设备与操作系统创建可执行程序，不需要局限于仅仅是运行 GCC 的平台。然而，这么做需要特殊的配置和安装，大多数 GCC 的安装，仅能针对它们的宿主系统编译程序。

GCC 不仅支持 C 的许多“方言”，也可以区别不同的 C 语言标准；也就是说，可以使用命令行选项来控制编译器在翻译源代码时应该遵循哪个 C 标准。

例如，当使用命令行参数`-std=c99`启动 GCC 时，编译器支持 C99 标准。GCC 对 C11 标准的支持是不完整的，尤其是涉及定义在头文件 threads.h 中的多线程函数。这是因为，GCC 的 C 链接库长期以来支持 POSIX 标准下与 C11 标准非常相似的多线程功能。针对更多这方面的细节，请参考 GCC 开发者 wiki 页面：[`gcc.gnu.org/wiki/C11Status`](https://gcc.gnu.org/wiki/C11Status)