# Python 函数关键字参数及用法

在定义 Python 函数时可定义形参（形式参数的意思），这些形参的值要等到调用时才能确定下来，由函数的调用者负责为形参传入参数值。简单来说，就是谁调用函数，谁负责传入参数值。

Python 函数的参数名不是无意义的，Python 允许在调用函数时通过名字来传入参数值。因此，Python 函数的参数名应该具有更好的语义，这样程序可以立刻明确传入函数的每个参数的含义。

按照形参位置传入的参数被称为位置参数。如果使用位置参数的方式来传入参数值，则必须严格按照走义函数时指定的顺序来传入参数值；如果根据参数名来传入参数值，则无须遵守定义形参的顺序，这种方式被称为关键字（keyword）参数。例如如下程序：

```
# 定义一个函数
def girth(width , height):
    print("width: ", width)
    print("height: ", height)
    return 2 * (width + height)
# 传统调用函数的方式，根据位置传入参数
print(girth(3.5, 4.8))
# 根据关键字参数来传入参数
print(girth(width = 3.5, height = 4.8))
# 使用关键字参数时可交换位置
print(girth(height = 4.8, width = 3.5))
# 部分使用关键字参数，部分使用位置参数
print(girth(3.5, height = 4.8))
```

上面程序定义了一个简单的 girth() 函数，该函数包含 width、height 两个参数，该函数与前面定义的函数并没有任何区别。

接下来在调用该函数时，既可使用传统的根据位置参数来调用（如上面程序中第 7 行代码所示），也可根据关键字参数来调用（如上面程序中第 9 行代码所示），在使用关键字参数调用时可交换参数的位置（如上面程序中第 11 行代码所示〕，还可混合使用位置参数和关键字参数（如上面程序中第 13 行代码所示）。

需要说明的是，如果希望在调用函数时混合使用关键字参数和位置参数，则关键字参数必须位于位置参数之后。换句话说，在关键字参数之后的只能是关键字参数。例如如下代码是错误的：

# 位置参数必须放在关键字参数之前，下面代码错误
print(girth(width = 3.5, 4.8))

运行上面代码，将会提示如下错误：

SyntaxError: positional argument follows keyword argument