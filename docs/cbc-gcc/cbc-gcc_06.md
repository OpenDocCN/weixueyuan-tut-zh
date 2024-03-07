# 检测是否已经安装 GCC 编译器

如果读者使用的是类 Unix 操作系统（Unix、Linux、Mac OS 等），很有可能已经安装好 GCC。如想知道是否如此，在命令行提示符后键入`cc --version`命令。如果已经安装好 GCC，并链接到名为 cc 的默认 C 编译器，就会看到编译器的版本号和版权信息：

$ cc --version
cc (GCC) 4.9.2
Copyright (C) 2014 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE.

> 在本教程示例中，如果输入行前面出现美元符号（$），表示这是命令行提示符。紧接其后的是命令行，可以通过控制台输入命令，启动 GCC（或者其他程序命令名称）。

有可能已经安装好 GCC，但是并没有链接到程序名称 cc。为解决这种情况，可以试着使用 GCC 的正式名称来调用它：

$ gcc --version

## 安装 GCC

如果还没有安装 GCC，可以咨询系统厂商，看看是否可以提供针对所用系统软件安装机制的 GCC 安装包。一些免费软件系统，如针对 Mac OS 的 MacPorts（[`www.macports.org/`](https://www.macports.org/)）和 Homebrew（[`brew.sh/`](http://brew.sh/)），针对 Windows 的 Cygwin（[`cygwin.org`](http://cygwin.org)）和 MinGW（[`www.mingw.org/`](http://www.mingw.org/)）也包括 GCC 安装包。

Cygwin 是 GNU 的一个扩展套件，它在 Windows 上提供一个面向 POSIX 标准的类 Unix 环境。

Cygwin 的基础是动态共享链接库 cygwin1.dll，当与底层 Windows 系统交互时，cygwin1.dll 向 Cygwin 程序提供类 Unix 系统函数。使用 GCC 编译的针对 Cygwin 的程序，也要求运行库 cygwin1.dll。

Cygwin 安装程序（[`cygwin.com`](https://cygwin.com)）最初只安装基本包，并加载一个包管理器，使用包管理器可以选择安装其他 Cygwin 软件，例如 gcc、make 和 gdb。你可以在任何时候运行安装程序来添加、移除和更新程序。

MinGW 也为 Windows 提供 GCC 编译器，但不同于 Gygwin，GCC 的 MinGW 版本生成 32 位 Windows 程序，而不需要特别的运行库。它的变体版本 MinGW-w64，也称为 MinGW64，能生成 64 位程序。可以通过网址 [`sourceforge.net/projects/mingw`](http://sourceforge.net/projects/mingw) 或 [`sourceforge.net/projects/mingw-w64`](http://sourceforge.net/projects/mingw-w64) 获取安装程序，安装最新版本的 MinGW。

注意，Cygwin 包管理器也允许安装 MinGW-GCC 包。GCC 因此具备类似交互编译器的能力，它在 Cygwin 上运行，但是生成不使用 cygwin1.dll 的 Windows 程序。

GCC 网站上列举了由第三方针对各种不同系统制作的 GCC 安装包，可以通过访问网址 [`gcc.gnu.org/install/binaries.html`](http://gcc.gnu.org/install/binaries.html) 来获取它们。此外，如果所用的操作系统已有另一个 C 编译器，那么可以从自由软件基金会（Free Software Foundation）获得 GCC 源代码，依据网址 [`gcc.gnu.org/install/`](http://gcc.gnu.org/install/) 上的指南一步步安装 GCC。