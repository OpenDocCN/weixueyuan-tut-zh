# Makefile ifeq、ifneq、ifdef 和 ifndef（条件判断）

> 原文：[`c.biancheng.net/view/7068.html`](http://c.biancheng.net/view/7068.html)

日常使用 Makefile 编译文件时，可能会遇到需要分条件执行的情况，比如在一个工程文件中，可编译的源文件很多，但是它们的类型是不相同的，所以编译文件使用的编译器也是不同的。手动编译去操作文件显然是不可行的（每个文件编译时需要注意的事项很多），所以 make 为我们提供了条件判断来解决这样的问题。

需要解决的问题：要根据判断，分条件执行语句。
条件语句的作用：条件语句可以根据一个变量的值来控制 make 执行或者时忽略 Makefile 的特定部分，条件语句可以是两个不同的变量或者是常量和变量之间的比较。

条件语句使用优点：Makefile 中使用条件控制可以做到处理的灵活性和高效性。

注意：条件语句只能用于控制 make 实际执行的 Makefile 文件部分，不能控制规则的 shell 命令执行的过程。

下面是条件判断中使用到的一些关键字：

| 关键字 | 功能 |
| ifeq | 判断参数是否不相等，相等为 true，不相等为 false。 |
| ifneq | 判断参数是否不相等，不相等为 true，相等为 false。 |
| ifdef | 判断是否有值，有值为 true，没有值为 false。 |
| ifndef | 判断是否有值，没有值为 true，有值为 false。 |

## ifeq 和 ifneq

条件判断的使用方式如下：

ifeq (ARG1, ARG2)
ifeq 'ARG1' 'ARG2'
ifeq "ARG1" "ARG2"
ifeq "ARG1" 'ARG2'
ifeq 'ARG1' "ARG2"

实例：

```

libs_for_gcc= -lgnu
normal_libs=
foo:$(objects)
ifeq($(CC),gcc)
    $(CC) -o foo $(objects) $(libs_for_gcc)
else
    $(CC) -o foo $(objects) $(noemal_libs)
endif
```

条件语句中使用到三个关键字“ifeq”、“else”、“endif”。其中：“ifeq”表示条件语句的开始，并指定一个比较条件（相等）。括号和关键字之间要使用空格分隔，两个参数之间要使用逗号分隔。参数中的变量引用在进行变量值比较的时候被展开。“ifeq”，后面的是条件满足的时候执行的，条件不满足忽略；“else”表示当条件不满足的时候执行的部分，不是所有的条件语句都要执行此部分；“endif”是判断语句结束标志，Makefile 中条件判断的结束都要有。

其实 "ifneq" 和 "ifeq" 的使用方法是完全相同的，只不过是满足条件后执行的语句正好相反。

上面的例子可以换一种更加简介的方式来写：

```

libs_for_gcc= -lgnu
normal_libs=
ifeq($(CC),gcc)
    libs=$(libs_for_gcc)
else
    libs=$(normal_libs)
endif
foo:$(objects)
    $(CC) -o foo $(objects) $(libs)
```

## ifdef 和 ifndef 

使用方式如下：

ifdef VARIABLE-NAME

它的主要功能是判断变量的值是不是为空，实例：

实例 1：

```

bar =
foo = $(bar)
all:
ifdef foo
    @echo yes
else
    @echo  no
endif

```

实例 2：

```

foo=
all:
ifdef foo
    @echo yes
else
    @echo  no
endif
```

通过两个实例对比说明：通过打印 "yes" 或 "no" 来演示执行的结果。我们执行 make 可以看到实例 1 打印的结果是 "yes" ，实例 2 打印的结果是 "no" 。其原因就是在实例 1 中，变量“foo”的定义是“foo = $(bar)”。虽然变量“bar”的值为空，但是“ifdef”的判断结果为真，这种方式判断显然是有不行的，因此当我们需要判断一个变量的值是否为空的时候需要使用“ifeq" 而不是“ifdef”。

注意：在 make 读取 Makefile 文件时计算表达式的值，并根据表达式的值决定判断语句中的哪一个部分作为此 Makefile 所要执行的内容。因此在条件表达式中不能使用自动化变量，自动化变量在规则命令执行时才有效，更不能将一个完整的条件判断语句分卸在两个不同的 Makefile 的文件中。在一个 Makefile 中使用指示符 "include" 包含另一个 Makefile 文件。