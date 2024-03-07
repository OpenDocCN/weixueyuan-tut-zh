# Python 函数默认参数设置（超级详细）

在某些情况下，程序需要在定义函数时为一个或多个形参指定默认值，这样在调用函数时就可以省略为该形参传入参数值，而是直接使用该形参的默认值。

为形参指定默认值的语法格式如下：

形参名 = 默认值

从上面的语法格式可以看出，形参的默认值紧跟在形参之后，中间以英文“=”隔开。

例如，如下程序为 name、message 形参指定了默认值：

```
# 为两个参数指定默认值
def say_hi(name = "孙悟空", message = "欢迎来到 C 语言中文网"):
    print(name, ", 您好")
    print("消息是：", message)
# 全部使用默认参数
say_hi()
# 只有 message 参数使用默认值
say_hi("白骨精")
# 两个参数都不使用默认值
say_hi("白骨精", "欢迎学习 Python")
# 只有 name 参数使用默认值
say_hi(message = "欢迎学习 Python")
```

上面程序中在定义 say_hi() 函数时为 name、message 形参指定了默认值，因此程序中第一次调用 say_hi() 函数时没有为 name、message 参数指定参数值，此时 name、message 参数将会使用其默认值。

程序第二次调用 say_hi() 函数时为 name 参数（使用位置参数）指定了参数值，此时 message 参数将会使用默认值。

程序第三次调用 say_hi() 函数时为 name、message 参数（使用位置参数）都指定了参数值，因此这两个参数都使用开发者传入的参数值。

程序第四次调用 say_hi() 函数时只为 message 参数（使用关键字参数）传入了参数值，此时 name 参数将使用默认值。

运行上面程序，可以看到如下输出结果：

孙悟空 , 您好
消息是： 欢迎来到 C 语言中文网
白骨精 , 您好
消息是： 欢迎来到 C 语言中文网
白骨精 , 您好
消息是： 欢迎学习 Python
孙悟空 , 您好
消息是： 欢迎学习 Python

从上面程序可以看出，如果只传入一个位置参数，由于该参数位于第一位，系统会将该参数值传给 name 参数。因此，我们不能按如下方式调用 say_hi() 函数：

say_hi("欢迎学习 Python")

上面调用时传入的“欢迎学习 Python”字符串将传给 name 参数，而不是 message 参数。

我们也不能按如下方式来调用 say_hi() 函数：

say_hi(name="白骨精", "欢迎学习 Python")

因为 Python 规定，关键字参数必须位于位置参数的后面。因此提示错误：positional argument follows keyword argument。

那么，我们能不能单纯地将上面两个参数交换位置呢？

#say_hi("欢迎学习 Python" , name="白骨精")

上面调用依然是错误的，因为第一个字符串没有指定关键字参数，因此将使用位置参数为 name 参数传入参数值，第二个参数使用关键字参数的形式再次为 name 参数传入参数值，这意味着两个参数值其实都会传给 name 参数，程序为 name 参数传入了多个参数值。因此提示错误：say hi() got multiple values for argument 'name'。

将函数调用改为如下两种形式是正确的：

```
say_hi("白骨精", message="欢迎学习 Python")
say_hi(name="白骨精", message="欢迎学习 Python")
```

上面第一行代码先使用位置参数为 name 参数传入参数值，再使用关键字参数为 message 参数传入参数值；第二行代码中的 name、message 参数都使用关键字参数传入参数值。

由于 Python 要求在调用函数时关键字参数必须位于位置参数的后面，因此在定义函数时指定了默认值的参数（关键字参数）必须在没有默认值的参数之后。例如如下代码：

```
# 定义一个打印三角形的函数，有默认值的参数必须放在后面
def printTriangle(char, height = 5) :
    for i in range(1, height + 1) :
        # 先打印一排空格
        for j in range(height - i) :
            print(' ', end = '')
        # 再打印一排特殊字符
        for j in range(2 * i - 1) :
            print(char, end = '')
        print()
printTriangle('@', 6)
printTriangle('#', height=7)
printTriangle(char = '*')
```

上面程序定义了二个 printTriangle() 函数，该函数的第一个 char 参数没有默认值，第二个 height 参数有默认值。

上面程序中第一次调用 printTriangle() 时，程序使用两个位置参数分别为 char、height 传入参数值，这当然是允许的；第二次调用 printTriangle() 时，第一个参数使用位置参数，那么该参数值将传给 char 参数，第二个参数使用关键字参数为 height 参数传入参数值，这也是允许的；第三次调用 printTriangle() 时，只使用关键字参数为 char 参数传入参数值，此时 height 参数将使用默认值，这是符合语法的。

再次强调，Python 要求将带默认值的参数定义在形参列表的最后。