# Makefile 嵌套执行 make

> 原文：[`c.biancheng.net/view/7120.html`](http://c.biancheng.net/view/7120.html)

这个章节主要讲解的是在 Makefile 中嵌套执行 make。我们都知道在一个大的工程文件中，不同的文件按照功能被划分到不同的模块中，也就说很多的源文件被放置在了不同的目录下。每个模块可能都会有自己的编译顺序和规则，如果在一个 Makefile 文件中描述所有模块的编译规则，就会很乱，执行时也会不方便，所以就需要在不同的模块中分别对它们的规则进行描述，也就是每一个模块都编写一个 Makefile 文件，这样不仅方便管理，而且可以迅速发现模块中的问题。这样我们只需要控制其他模块中的 Makefile 就可以实现总体的控制，这就是 make 的嵌套执行。

如何来使用呢？举例说明如下：

```

subsystem:
    cd subdir && $(MAKE)
```

这个例子可以这样来理解，在当前目录下有一个目录文件 subdir 和一个 Makefile 文件，子目录 subdir 文件下还有一个 Makefile 文件，这个文件是用来描述这个子目录文件的编译规则。使用时只需要在最外层的目录中执行 make 命令，当命令执行到上述的规则时，程序会进入到子目录中执行 make。这就是嵌套执行 make，我们把最外层的 Makefile 称为是总控 Makefile。

上述的规则也可以换成另外一种写法：

```

subsystem
    $(MAKE) -C subdir
```

在 make 的嵌套执行中，我们需要了解一个变量 "CURDIR"，此变量代表 make 的工作目录。当使用 make 的选项 "-C" 的时候，命令就会进入指定的目录中，然后此变量就会被重新赋值。总之，如果在 Makefile 中没有对此变量进行显式的赋值操作，那么它就表示 make 的工作目录。我们也可以在 Makefile 中为这个变量赋一个新的值，当然重新赋值后这个变量将不再代表 make 的工作目录。

#### export 的使用

使用 make 嵌套执行的时候，变量是否传递也是我们需要注意的。如果需要变量的传递，那么可以这样来使用：

export <variable>

如果不需要那么可以这样来写：

unexport <variable>

<variable>是变量的名字，不需要使用 "$" 这个字符。如果所有的变量都需要传递，那么只需要使用 "export" 就可以，不需要添加变量的名字。

Makefile 中还有两个变量不管是不是使用关键字 "export" 声明，它们总会传递到下层的 Makefile 中。这两个变量分别是 SHELL 和 MAKEFLAGS，特别是 MAKEFLAGS 变量，包含了 make 的参数信息。如果执行总控 Makefile 时，make 命令带有参数或者在上层的 Makefile 中定义了这个变量，那么 MAKEFLAGS 变量的值将会是 make 命令传递的参数，并且会传递到下层的 Makefile 中，这是一个系统级别的环境变量。

make 命令中有几个参数选项并不传递，它们是:"-C"、"-f"、"-o"、"-h" 和 "-W"。如果我们不想传递 MAKEFLAGS 变量的值，在 Makefile 中可以这样来写：

```

subsystem:
    cd subdir && $(MAKE) MAKEFLAGS=
```