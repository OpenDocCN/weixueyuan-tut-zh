# Makefile 中的其它常用函数

> 原文：[`c.biancheng.net/view/7095.html`](http://c.biancheng.net/view/7095.html)

之前学习过了 Makefile 中的字符串操作文件和文件名操作函数，我们今天再来看一下 Makefile 中的其他的函数。以下是这些函数的详细说明。

#### $(foreach <var>,<list>,<text>)

函数的功能是：把参数`<list>`中的单词逐一取出放到参数 `<var>` 所指定的变量中，然后再执行`<text>`所包含的表达式。每一次 `<text>` 会返回一个字符串，循环过程中，`<text>` 的返所返回的每个字符串会以空格分割，最后当整个循环结束的时候，`<text>` 所返回的每个字符串所组成的整个字符串（以空格分隔）将会是 foreach 函数的返回值。所以`<var>`最好是一个变量名，`<list>` 可以是一个表达式，而`<text>`中一般会只用 `<var>`这个参数来一次枚举`<list>`中的单词。

实例：

```

name:=a b c d
files:=$(foreach n,$(names),$(n).o)
all:
    @echo $(files)
```

执行 make 命令，我们得到的值是“a.o b.o c.o d.o”。

注意，foreach 中的 `<var>` 参数是一个临时的局部变量，foreach 函数执行完后，参数`<var>`的变量将不再作用，其作用域只在 foreach 函数当中。

#### $(if <condition>,<then-part>)或(if<condition>,<then-part>,<else-part>)

可见，if 函数可以包含`else`部分，或者是不包含，即 if 函数的参数可以是两个，也可以是三个。`condition`参数是 if 表达式，如果其返回的是非空的字符串，那么这个表达式就相当于返回真，于是，`then-part`就会被计算，否则`else-part`会被计算。

而 if 函数的返回值是：如果`condition`为真（非空字符串），那么`then-part`会是整个函数的返回值。如果`condition`为假（空字符串），那么`else-part`将会是这个函数的返回值。此时如果`else-part`没有被定义，那么整个函数返回空字串符。所以，`then-part`和`else-part`只会有一个被计算。

实例：

```

OBJ:=foo.c
OBJ:=$(if $(OBJ),$(OBJ),main.c)
all:
      @echo $(OBJ)
```

执行 make 命令我们可以得到函数的值是 foo.c，如果变量 OBJ 的值为空的话，我们得到的 OBJ 的值就是`main.c`。

#### $(call <expression>,<parm1>,<parm2>,<parm3>,...)

call 函数是唯一一个可以用来创建新的参数化的函数。我们可以用来写一个非常复杂的表达式，这个表达式中，我们可以定义很多的参数，然后你可以用 call 函数来向这个表达式传递参数。

当 make 执行这个函数的时候，`expression`参数中的变量$(1)、$(2)、$(3)等，会被参数`parm1`，`parm2`，`parm3`依次取代。而`expression`的返回值就是 call 函数的返回值。

实例 1：

```

reverse = $(1) $(2)
foo = $(call reverse,a,b)
all：
      @echo $(foo)
```

那么，foo 的值就是“a b”。当然，参数的次序可以是自定义的，不一定是顺序的，

实例 2：

```

reverse = $(2) $(1)
foo = $(call reverse,a,b)
all:
      @echo $(foo)

```

此时的 foo 的值就是“b a”。

#### $(origin <variable>)

origin 函数不像其他的函数，它并不操作变量的值，它只是告诉你这个变量是哪里来的。

注意： variable 是变量的名字，不应该是引用，所以最好不要在 variable 中使用“$”字符。origin 函数会员其返回值来告诉你这个变量的“出生情况”。

下面是 origin 函数返回值：

*   “undefined”：如果<variable>从来没有定义过，函数将返回这个值。
*   “default”：如果<variable>是一个默认的定义，比如说“CC”这个变量。
*   “environment”：如果<variable>是一个环境变量并且当 Makefile 被执行的时候，“-e”参数没有被打开。
*   “file”：如果<variable>这个变量被定义在 Makefile 中，将会返回这个值。
*   “command line”：如果<variable>这个变量是被命令执行的，将会被返回。
*   “override”：如果<variable>是被 override 指示符重新定义的。
*   “automatic”：如果<variable>是一个命令运行中的自动化变量。

这些信息对于我们编写 Makefile 是非常有用的，例如假设我们有一个 Makefile ，其包含了一个定义文件`Make.def`，在`Make.def`中定义了一个变量`bletch`，而我们的环境变量中也有一个环境变量`bletch`，我们想去判断一下这个变量是不是环境变量，如果是我们就把它重定义了。如果是非环境变量，那么我们就不重新定义它。于是，我们在 Makefile 中，可以这样写：

```

ifdef bletch
ifeq "$(origin bletch)" "environment"
bletch = barf,gag,etc
endif
endif
```

当然，使用`override`关键字不就可以重新定义环境中的变量了吗，为什么需要使用这样的步骤？是的，我们用`override`是可以达到这样的效果的，可是`override`会把从命令行定义的变量也覆盖了，而我们只想重新定义环境传来的，而不是重新定义命令行传来的。