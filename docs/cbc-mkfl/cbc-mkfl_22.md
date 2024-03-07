# Makefile 变量的高级用法

> 原文：[`c.biancheng.net/view/7139.html`](http://c.biancheng.net/view/7139.html)

我们之前已经学习过变量的定义和基本的赋值运算，我们可以更深入的了解一下变量的一些高级的用法。高级使用方法有两种：一种是变量的替换引用，一种是变量的嵌套引用。这是我们在使用的时候比较常见的两种使用方法，我们来具体分析使用一下变量的这两种高级的用法。

## 变量的替换引用

我们定义变量的目的是为了简化我们的书写格式，代替我们在代码中频繁出现且冗杂的部分。它可以出现在我们规则的目标中，也可以是我们规则的依赖中。我们使用的时候会经常的对它的值（表示的字符串）进行操作。遇到这样的问题我们可能会想到我们的字符串操作函数，比如 "patsubst" 就是我们经常使用的。但是我们使用变量同样可以解决这样的问题，我们通过下面的例子来具体的分析一下。
实例：

```

foo:=a.c b.c d.c
obj:=$(foo:.c=.o)
All:
    @echo $(obj)
```

这段代码实现的功能是字符串的后缀名的替换，把变量 foo 中所有的以 .c 结尾的字符串全部替换成 .o 结尾的字符串。我们在 Makefile 中这样写，然后再 shell 命令行执行 make 命令，就可以看到打印出来的是 "a.o b.o d.o" ，实现了文件名后缀的替换。

注意：括号中的变量使用的是变量名而不是变量名的引用，变量名的后面要使用冒号和参数选项分开，表达式中间不能使用空格。第二个变量 obj 是对整体的引用。

上面的例子我们可以换一种更加通用的方式来写，代码展示如下：

```

foo:=a.c b.c d.c
obj:=$(foo:%.c=%.o)
All:
    @echo $(obj)

```

我们在 shell 中执行 make 命令，发现结果是相同的。

对比上面的实例我们可以看到，表达式中使用了 "%" 这个字符，这个字符的含义就是自动匹配一个或多个字符。在开发的过程中，我们通常会使用这种方式来进行变量替换引用的操作。

为什么这种方式比第一种方式更加实用呢？我们在实际使用的过程中，我们对变量值的操作不只是修改其中的一个部分，甚至是改变其中的多个，那么第一种方式就不能实现了。我们来看一下这种情况：

```

foo:=a123c a1234c a12345c
obj:=$(foo:a%c=x%y)
All:
    @echo $(obj)
```

我们可以看到这个例子中我们操作的是两个不连续的部分，我们执行 make 后打印的值是 "x123y x1234y x12345y"，这种情况下我们使用第一种情况就不能实现，所以第二种的使用更全面。

## 变量的嵌套使用

变量的嵌套引用的具体含义是这样的，我们可以在一个变量的赋值中引用其他的变量，并且引用变量的数量和和次数是不限制的。下面我们通过几个实例来说明一下。
实例 1：

```

foo:=test
var:=$(foo)
All:
    @echo $(var)

```

这种用法是最常见的使用方法，打印出 var 的值就是 test。我们可以认为是一层的嵌套引用。
实例 2：

```

foo=bar
var=test
var:=$($(foo))
All:
    @echo $(var)

```

我们再去执行 make 命令的时候得到的结果也是 test，我们可以来分析一下这段代码执行的过程：$(foo) 代表的字符串是 bar，我们也定义了变量 bar，所以我们可以对 bar 进行引用，变量 bar 表示的值是 test，所以对 bar 的引用就是 test，所以最终 var 的值就是 test。这是变量的二层嵌套执行，当然我们还可以使用三层的嵌套执行，写法跟上面的方式是一样的。嵌套的层数也可以更多，但是不提倡使用。

我们再去使用变量的时候，我们并不是只能引用一个变量，可以有多个变量的引用，还可以包含很多的变量还可以是一些文本字符。我们可以通过一些例子来说明一下。
实例 4：

```

first_pass=hello
bar=first
var:=$(bar)_pass
all:
    @echo $(var)
```

在命令行执行 make 我们可以得到 var 的值是 hello。这是变量嵌套引用的时候可以包含其它字符的使用情况。
实例 5：

```

first_pass=hello
bar=first
foo=pass
var:=$(bar)_$(foo)
all:
    @echo $(var)
```

这个实例跟上面实例的运行结果是一样的。我们可以看到这个实例中使用了两个变量的引用还有其它的字符。

变量的嵌套引用和我们的变量的递归赋值的区别：嵌套引用的使用方法就是用一个变量表示另外一个变量，然后进行多层的引用。而递归展开的变量表示当一个变量存在对其它变量的引用时，对这变量替换的方式。递归展开在另外一个角度描述了这个变量在定义是赋予它的一个属性或者风格。并且我们可以在定义个一个递归展开式的变量时使用套嵌引用的方式，但是建议你的实际编写 Makefile 时要尽量避免这种复杂的用法。

在实际使用的过程中变量的第一种用法经常使用的，第二种用法我们很少使用，应该说是尽量避免使用变量的嵌套引用。在必须要使用的时候我们应该做到嵌套的层数是越少越好的。因为使用这种方法表达会比较的复杂，如果条理不清楚的话我们就会出错。并且在给其他人看的时候也会不容易理解。