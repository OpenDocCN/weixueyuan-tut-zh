# Python 异常处理

> 原文：[`www.weixueyuan.net/python/exceptions/`](http://www.weixueyuan.net/python/exceptions/)

异常处理也是现代编程语言的基本特性，使用异常处理可以将正常分支和异常分支分割开来，而不像 C 语言那样，正常分支和异常分支是混在一起的。

Python 中，异常就是一个特殊的对象，通过该异常对象可以给异常处理函数传递异常发生时的信息。

本章介绍如何捕捉异常、得到异常信息、忽略某些异常、抛出异常、自定义异常对象，以及异常处理在 Python 2 和 Python 3 中的异同，最后介绍常见的系统定义异常。