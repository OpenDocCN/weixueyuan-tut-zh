# Python lambda 表达式及用法

lambda 表达式是现代编程语言争相引入的一种语法，如果说函数是命名的、方便复用的代码块，那么 lambda 表达式则是功能更灵活的代码块，它可以在程序中被传递和调用。

## 回顾局部函数

回顾《Python 函数高级用法》一节中，get_math_func() 函数将返回三个局部函数之一。该函数代码如下：

def get_math_func(type) :
    # 定义三个局部函数
    ...
    # 返回局部函数
    if type == "square" :
        return square
    if type == "cube" :
        return cube
    else:
        return factorial

由于局部函数的作用域默认仅停留在其封闭函数之内，因此这三个局部函数的函数名的作用太有限了，即仅仅是在程序的 if 语句中作为返回值使用。一旦离开了 get_math_func() 函数体，这三个局部函数的函数名就失去了意义。

既然局部函数的函数名没有太大的意义，那么就考虑使用 lambda 表达式来简化局部函数的写法。

## 使用 lambda 表达式代替局部函数

如果使用 lambda 表达式来简化 get_math_func() 函数，则可以将程序改写成如下形式：

```
def get_math_func(type) :
    result=1
    # 该函数返回的是 Lambda 表达式
    if type == 'square':
        return lambda n: n * n  # ①
    elif type == 'cube':
        return lambda n: n * n * n  # ②
    else:
        return lambda n: (1 + n) * n / 2 # ③
# 调用 get_math_func()，程序返回一个嵌套函数
math_func = get_math_func("cube")
print(math_func(5)) # 输出 125
math_func = get_math_func("square")
print(math_func(5)) # 输出 25
math_func = get_math_func("other")
print(math_func(5)) # 输出 15.0
```

在上面代码中，return 后面的部分使用 lambda 关键字定义的就是 lambda 表达式，Python 要求 lambda 表达式只能是单行表达式。

注意：由于 lambda 表达式只能是单行表达式，不允许使用更复杂的函数形式，因此上面 ③ 号代码处改为计算 1+2+3+…+n 的总和。

lambda 表达式的语法格式如下：

lambda [parameter_list] : 表达式

从上面的语法格式可以看出 lambda 表达式的几个要点：

*   lambda 表达式必须使用 lambda 关键字定义。
*   在 lambda 关键字之后、冒号左边的是参数列表，可以没有参数，也可以有多个参数。如果有多个参数，则需要用逗号隔开，冒号右边是该 lambda 表达式的返回值。

实际上，lambda 表达式的本质就是匿名的、单行函数体的函数。因此，lambda 表达式可以写成函数的形式。例如，对于如下 lambda 表达式：

lambda x , y:x + y

可改写为如下函数形式：

def add(x, y): return x+ y

上面定义函数时使用了简化语法：当函数体只有一行代码时，可以直接把函数体的代码放在与函数头同一行。

总体来说，函数比 lambda 表达式的适应性更强，lambda 表达式只能创建简单的函数对象（它只适合函数体为单行的情形）。但 lambda 表达式依然有如下两个用途：

*   对于单行函数，使用 lambda 表达式可以省去定义函数的过程，让代码更加简洁。
*   对于不需要多次复用的函数，使用 lambda 表达式可以在用完之后立即释放，提高了性能。

下面代码示范了通过 lambda 表达式来调用 Python 内置的 map() 函数：

```
# 传入计算平方的 lambda 表达式作为参数
x = map(lambda x: x*x , range(8))
print([e for e in x]) # [0, 1, 4, 9, 16, 25, 36, 49]
# 传入计算平方的 lambda 表达式作为参数
y = map(lambda x: x*x if x % 2 == 0 else 0, range(8))
print([e for e in y]) # [0, 0, 4, 0, 16, 0, 36, 0]
```

正如从上面代码所看到的，内置的 map() 函数的第一个参数需要传入函数，此处传入了函数的简化形式：lambda 表达式，这样程序更加简洁，而且性能更好。

## 总结

本节所介绍的 lambda 表达式是 Python 编程的核心机制之一。Python 语言既支持面向过程编程，也支持面向对象编程。而 lambda 表达式是 Python 面向过程编程的语法基础，因此读者必须引起重视。

Python 的 lambda 表达式只是单行函数的简化版本，因此 lambda 表达式的功能比较简单。