# Makefile 常用文件名操作函数

> 原文：[`c.biancheng.net/view/7081.html`](http://c.biancheng.net/view/7081.html)

我们在编写 Makefile 的时候，很多情况下需要对文件名进行操作。例如获取文件的路径，去除文件的路径，取出文件前缀或后缀等等。当遇到这样的问题的时手动修改是不太可能的，因为文件可能会很多，而且 Makefile 中操作文件名可能不止一次。所以 Makefile 给我们提供了相应的函数去实现文件名的操作。

注意：下面的每个函数的参数字符串都会被当作或是一个系列的文件名来看待。

1\. 取目录函数，函数使用格式如下：

$(dir <names>)

函数说明：函数的功能是从文件名序列 names 中取出目录部分，如果没有 names 中没有 "/" ，取出的值为 "./" 。返回值为目录部分，指的是最后一个反斜杠之前的部分。如果没有反斜杠将返回“./”。实例：

```

OBJ=$(dir src/foo.c hacks)
all:
    @echo $(OBJ)
```

执行 make 命令，我们可以得到的值是“src/ ./”。提取文件 foo.c 的路径是 "/src" 和文件 hacks 的路径 "./"。

2\. 取文件函数，函数使用格式如下：

$(notdir <names>)

函数说明：函数的功能是从文件名序列 names 中取出非目录的部分。非目录的部分是最后一个反斜杠之后的部分。返回值为文件非目录的部分。实例：

```

OBJ=$(notdir src/foo.c hacks)
all:
    @echo $(OBJ)
```

执行 make 命令，我们可以得到的值是“foo.c hacks”。

3\. 取后缀名函数，函数使用格式如下：

$(suffix <names>)

函数说明：函数的功能是从文件名序列中 names 中取出各个文件的后缀名。返回值为文件名序列 names 中的后缀序列，如果文件没有后缀名，则返回空字符串。实例：

```

OBJ=$(suffix src/foo.c hacks)
all:
    @echo $(OBJ)

```

执行 make 命令，我们得到的值是“.c ”。文件 "hacks" 没有后缀名，所以返回的是空值。

4\. 取前缀函数，函数使用格式如下：

$(basename <names>)

函数说明：函数的功能是从文件名序列 names 中取出各个文件名的前缀部分。返回值为被取出来的文件的前缀名，如果文件没有前缀名则返回空的字符串。实例：

```

OBJ=$(notdir src/foo.c hacks)
all:
    @echo $(OBJ)
```

执行 make 命令，我们可以得到值是“src/foo hacks”。获取的是文件的前缀名，包含文件路径的部分。

5\. 添加后缀名函数，函数使用格式如下：

$(addsuffix <suffix>,<names>)

函数说明：函数的功能是把后缀 suffix 加到 names 中的每个单词后面。返回值为添加上后缀的文件名序列。实例：

```

OBJ=$(addsuffix .c,src/foo.c hacks)
all:
    @echo $(OBJ)

```

执行 make 后我们可以得到“sec/foo.c.c hack.c”。我们可以看到如果文件名存在后缀名，依然会加上。

6\. 添加前缀名函数，函数使用格式如下：

$(addperfix <prefix>,<names>)

函数说明：函数的功能是把前缀 prefix 加到 names 中的每个单词的前面。返回值为添加上前缀的文件名序列。实例：

```

OBJ=$(addprefix src/, foo.c hacks)
all:
    @echo $(OBJ)
```

执行 make 命令，我们可以得到值是 "src/foo.c src/hacks" 。我们可以使用这个函数给我们的文件添加路径。

7\. 链接函数，函数使用格式如下：

$(join <list1>,<list2>)

函数说明：函数功能是把 list2 中的单词对应的拼接到 list1 的后面。如果 list1 的单词要比 list2 的多，那么，list1 中多出来的单词将保持原样，如果 list1 中的单词要比 list2 中的单词少，那么 list2 中多出来的单词将保持原样。返回值为拼接好的字符串。实例：

```

OBJ=$(join src car,abc zxc qwe)
all:
    @echo $(OBJ)
```

执行 make 命令，我们可以得到的值是“srcabc carzxc qwe”。很显然 `<list1>` 中的文件名比`<list2>`的少，所以多出来的保持不变。

8\. 获取匹配模式文件名函数，命令使用格式如下：

$(wildcard PATTERN)

函数说明：函数的功能是列出当前目录下所有符合模式的 PATTERN 格式的文件名。返回值为空格分隔并且存在当前目录下的所有符合模式 PATTERN 的文件名。实例：

```

OBJ=$(wildcard *.c  *.h)
all:
    @echo $(OBJ)
```

执行 make 命令，可以得到当前函数下所有的 ".c " 和  ".h"  结尾的文件。这个函数通常跟的通配符 "*" 连用，使用在依赖规则的描述的时候被展开（在这里我们的例子如果没有 wildcard 函数，我们的运行结果也是这样，"echo" 属于 shell 命令，在使用通配符的时通配符自动展开，我们这里只是相要说明一下这个函数在使用时，如果通过引用变量出现在规则中要被使用）。