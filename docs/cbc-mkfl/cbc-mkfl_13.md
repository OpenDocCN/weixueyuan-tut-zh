# Makefile 常用字符串处理函数

> 原文：[`c.biancheng.net/view/7093.html`](http://c.biancheng.net/view/7093.html)

学习使用函数之前，先来看一下函数的语法结构。函数的调用和变量的调用很像。引用变量的格式为`$(变量名)`，函数调用的格式如下：

$(<function> <arguments>)    或者是     ${<function> <arguments>}

其中，function 是函数名，arguments 是函数的参数，参数之间要用逗号分隔开。而参数和函数名之间使用空格分开。调用函数的时候要使用字符“$”，后面可以跟小括号也可以使用花括号。这个其实我们并不陌生，我们之前使用过许多的函数，比如说展开通配符的函数 wildcard，以及字符串替换的函数 patsubst ，Makefile 中函数并不是很多。

今天主要讲的是字符串处理函数，这些都是我们经常使用到的函数，下面是对函数详细的介绍。

1\. 模式字符串替换函数，函数使用格式如下：

$(patsubst <pattern>,<replacement>,<text>)

函数说明：函数功能是查找 text 中的单词是否符合模式 pattern，如果匹配的话，则用 replacement 替换。返回值为替换后的新字符串。实例：

```

OBJ=$(patsubst %.c,%.o,1.c 2.c 3.c)
all:
    @echo $(OBJ)

```

执行 make 命令，我们可以得到的值是 "1.o 2.o 3.o"，这些都是替换后的值。

2\. 字符串替换函数，函数使用格式如下：

$(subst <from>,<to>,<text>)

函数说明：函数的功能是把字符串中的 form 替换成 to，返回值为替换后的新字符串。实例：

```

OBJ=$(subst ee,EE,feet on the street)
all:
    @echo $(OBJ)
```

执行 make 命令，我们得到的值是“fEEt on the strEEt”。

3\. 去空格函数，函数使用格式如下：

$(strip <string>)

函数说明：函数的功能是去掉字符串的开头和结尾的字符串，并且将其中的多个连续的空格合并成为一个空格。返回值为去掉空格后的字符串。实例：

```

OBJ=$(strip    a       b c)
all:
    @echo $(OBJ)
```

执行完 make 之后，结果是“a b c”。这个只是除去开头和结尾的空格字符，并且将字符串中的空格合并成为一个空格。

4\. 查找字符串函数，函数使用格式如下：

$(findstring <find>,<in>)

函数说明：函数的功能是查找  in 中的 find ,如果我们查找的目标字符串存在。返回值为目标字符串，如果不存在就返回空。实例：

```

OBJ=$(findstring a,a b c)
all:
    @echo $(OBJ)
```

执行 make 命令，得到的返回的结果就是 "a"。

5\. 过滤函数，函数使用格式如下：

$(filter <pattern>,<text>)

函数说明：函数的功能是过滤出 text 中符合模式 pattern 的字符串，可以有多个 pattern 。返回值为过滤后的字符串。实例：

```

OBJ=$(filter %.c %.o,1.c 2.o 3.s)
all:
    @echo $(OBJ)
```

执行 make 命令，我们得到的值是“1.c 2.o”。

6\. 反过滤函数，函数使用格式如下：

$(filter-out <pattern>,<text>)

函数说明：函数的功能是功能和 filter 函数正好相反，但是用法相同。去除符合模式  pattern 的字符串，保留符合的字符串。返回值是保留的字符串。实例：

```

OBJ=$(filter-out 1.c 2.o ,1.o 2.c 3.s)
all：
    @echo $(OBJ)
```

执行 make 命令，打印的结果是“3.s”。

7\. 排序函数，函数使用格式如下：

$(sort <list>)

函数说明：函数的功能是将 `<list>` 中的单词排序（升序）。返回值为排列后的字符串。实例：

```

OBJ=$(sort foo bar foo lost)
all:
    @echo $(OBJ)
```

执行 make 命令，我们得到的值是“bar foo lost”。

注意：sort 会去除重复的字符串。
8\. 取单词函数，函数使用格式如下：

$(word <n>,<text>)

函数说明：函数的功能是取出函数 `<text>` 中的第 n 个单词。返回值为我们取出的第 n 个单词。实例：

```

OBJ=$(word 2,1.c 2.c 3.c)
all:
    @echo $(OBJ)
```

执行 make 命令，我们得到的值是“2.c”。