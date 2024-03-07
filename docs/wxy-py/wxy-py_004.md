# Python 的版本

> 原文：[`www.weixueyuan.net/a/357.html`](http://www.weixueyuan.net/a/357.html)

Python 作为一种语言，它也是随时间而逐步演进的：

*   早期版本的 Python 被称作是 Python 1；
*   在 2000 年，Python 2 的第一个版本发布了，它目前仍在广泛使用中；
*   2008 年 Python 3 的第一个版本发布，它是目前的最新版本。

不同版本的 Python 语法有所不同，有些代码使用了某个特定版本的语法，则该代码就被绑定到特定的 Python 版本上了。

我们可能听说过 C++11、C99 这些名词。C++11 表示 2011 年发布的 C++ 语言版本，C99 表示 1999 年发布的 C 语言版本。Java 语言也一样，目前广泛使用的版本包括 Java 7、Java 8 和 Java 10 等。

类似地，Python 语言也是有其版本的。最初发布的是 Python 1，但是这个版本并没有广泛流行起来。现在 Python 语言主要分为两个大的版本，Python 2 和 Python 3。

## Python 2 和 Python 3

Python 2 于 2000 年 10 月 16 日发布，其最后一个版本是 2.7。Python 2.7 在 2020 年 1 月 1 日已经无法得到 Python 社区的支持，所以进入到了类似于 Windows XP 的状态。

Python 3 于 2008 年 12 月 3 日发布，目前的版本是 3.7。Python 3 是目前最活跃的版本，基本上新开发的 Python 代码都会支持 Python 3。

Python 4 是未来的版本，目前还处于萌芽状态，至今没有相关发布，预计会在 2023 年左右发布第一个版本。由于目前还没有软件支持 Python 4，所以本教程就不涉及 Python 4 的相关内容。

Python 3 和 Python 2 并不是完全兼容的，即在 Python 2 中可以运行的代码并不一定可以在 Python 3 中运行。这是一个很让人头疼的问题，这意味着很多老的项目需要经过重构才能迁移到 Python 3 上来。

当然，Python 社区也意识到了这个问题，所以在 Python 3 中也提供了一些工具，如 2to3，这些工具可以帮助用户将 Python 2 编写的代码转换成 Python 3 编写的代码。

现阶段来看，多数 Python 库都完成了向 Python 3 迁移的任务，本教程的代码也将以 Python 3 为主，同时兼顾 Python 2 的用法。建议读者安装 Python 3.5 和 Python 2.7 来练习教程中的例子代码。