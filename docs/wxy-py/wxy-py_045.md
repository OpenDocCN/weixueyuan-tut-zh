# Python lambda 表达式（匿名函数）的用法

> 原文：[`www.weixueyuan.net/a/614.html`](http://www.weixueyuan.net/a/614.html)

lambda 函数是一种特殊的函数，它只能包含一句话，所以不太可能完成复杂的任务。而且一般情况下 lambda 函数是临时性的，并不会长期存在，也不会在大范围内有效。

定义一个 lambda 函数的方式如下：

lambda 参数列表:单行语句

一个简单的例子就是求和，可以定义如下：

lambda x, y: x+y

虽然多数时候 lambda 函数是作为一个匿名函数来使用的，但还是可以将其值赋值为变量，这时它就更像普通的函数了，只是函数体只有一句话而已。下面演示了这种用法。

```

>>> a = lambda x, y: x+y
>>> type(a)
<class 'function'>
>>> a
<function <lambda> at 0x0000000002974378>
>>> a(1, 2)
3
```

在所有可以使用函数的地方都可以使用 lambda 函数，如函数式编程、闭包等场合。下面举两个应用的例子。

在排序函数 sorted() 中，有一个参数 key，该参数就需要一个函数，其对所有的元素都调用这个函数，并对返回值进行排序。最简单的用法就是定义一个函数，并将其作为参数 key 传入函数 sorted()。

下面是一个最简单的例子，在这个例子中，这个新定义的函数对输入不做任何处理就直接返回，也就是 f(x)=x。

```

>>> a = [-1, 2, -8, 10]
>>> sorted(a, key=lambda x: x)
[-8, -1, 2, 10]
```

如果希望对输入元素的绝对值进行排序，则可以按如下方式来定义 key 参数：

```

>>> a = [-1, 2, -8, 10]
>>> sorted(a, key=lambda x: abs(x))
[-1, 2, -8, 10]
```

如果希望以该元素与 8 的差值来排序，则可以按如下方式来定义 key 参数：

```

>>> a = [-1, 2, -8, 10]
>>> sorted(a, key=lambda x: abs(x-8))
[10, 2, -1, -8]
```

> 这里需要注意的是，lambda 函数是函数的一个特例，所有可以使用 lambda 函数的地方都可以使用普通函数来代替。