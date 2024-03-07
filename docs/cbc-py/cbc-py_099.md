# 第九章 Python 模块和包

前面在介绍异常时已经用到了系统提供的 sys、 OS、 traceback 这三个模块，可能已有读者对模块这个“神秘”的东西感到很好奇，Python 每次导入模块之后，Python 似乎就增加了某种特别的功能，模块怎么这么厉害呢？这些模块到底是从哪里来的？

本章将会带着大家去真正了解模块这个强大的工具。实际上，Python 语言之所以能被广泛应用于各行各业，在很大程度上得益于它的模块化系统。在 Python 的标准安装中包含了一组自带的模块，这些模块被称为“标准库”。

Python 3 的标准库请参考 [`docs.python.org/3/library/index.html`](https://docs.python.org/3/library/index.html)。

更重要的是，开发者完全可以根据自己的需要不断地为 Python 增加扩展库。各行各业的 Python 用户贡献了大量的扩展库，这些扩展库极大地丰富了 Python 的功能，这些扩展库从某种程度上也形成了 Python 的“生态圈”。