# Python 函数使用方法（高级用法）

Python 的函数是“一等公民”，因此函数本身也是一个对象，函数既可用于赋值，也可用作其他函数的参数，还可作为其他函数的返回值。

## 使用函数变量

Python 的函数也是一种值：所有函数都是 function 对象，这意味着可以把函数本身赋值给变量，就像把整数、浮点数、列表、元组赋值给变量一样。

当把函数赋值给变量之后，接下来程序也可通过该变量来调用函数。例如如下代码：

```
# 定义一个计算乘方的函数
def pow(base, exponent) :
    result = 1
    for i in range(1, exponent + 1) :
        result *= base
    return result
# 将 pow 函数赋值给 my_fun，则 my_fun 可当成 pow 使用
my_fun = pow
print(my_fun(3 , 4)) # 输出 81
# 定义一个计算面积的函数
def area(width, height) :
    return width * height
# 将 area 函数赋值给 my_fun，则 my_fun 可当成 area 使用
my_fun = area
print(my_fun(3, 4)) # 输出 12
```

从上面代码可以看出，程序依次将 pow()、area() 函数赋值给 my_fun 变量，接下来即可通过 my_fun 变量分别调用 pow()、area() 函数。

其实 python 已经内置了计算乘方的方法，因此此处的 pow() 函数并没有太大的实际意义，只是作为示范使用。

通过对 my_fun 变量赋值不同的函数，可以让 my_fun 在不同的时间指向不同的函数，从而让程序更加灵活。由此可见，使用函数变量的好处是让程序更加灵活。

除此之外，程序还可使用函数作为另一个函数的形参和（或）返回值。

## 使用函数作为函数形参

有时候需要定义一个函数，该函数的大部分计算逻辑都能确定，但某些处理逻辑暂时无法确定，这意昧着某些程序代码需要动态改变，如果希望调用函数时能动态传入这些代码，那么就需要在函数中定义函数形参，这样即可在调用该函数时传入不同的函数作为参数，从而动态改变这段代码。

Python 支持像使用其他参数一样使用函数参数，例如如下程序：

```
# 定义函数类型的形参，其中 fn 是一个函数
def map(data, fn) :   
    result = []
    # 遍历 data 列表中每个元素，并用 fn 函数对每个元素进行计算
    # 然后将计算结果作为新数组的元素
    for e in data :
        result.append(fn(e))
    return result
# 定义一个计算平方的函数
def square(n) :
    return n * n
# 定义一个计算立方的函数
def cube(n) :
    return n * n * n
# 定义一个计算阶乘的函数
def factorial(n) :
    result = 1
    for index in range(2, n + 1) :
        result *= index
    return result
data = [3 , 4 , 9 , 5, 8]
print("原数据: ", data)
# 下面程序代码 3 次调用 map()函数，每次调用时传入不同的函数
print("计算数组元素的平方")
print(map(data , square))
print("计算数组元素的立方")
print(map(data , cube))
print("计算数组元素的阶乘")
print(map(data , factorial))
```

上面程序中定义了一个 map() 函数，该函数的第二个参数是一个函数类型的参数，这意味着每次调用函数时可以动态传入一个函数，随着实际传入函数的改变，就可以动态改变 map() 函数中的部分计算代码。

Pthon 3 本身也提供了一个 map() 函数，Python 3 内置的 map() 函数的功能和此处定义的 map() 函数功能类似，但更加强大。

接下来的三行粗体字代码调用了 map() 函数三次，三次调用依次传入了 square、cube、factorial 函数作为参数，这样每次调用 map() 函数时实际的执行代码是有区别的。

编译、运行上面程序，可以看到如下输出结果：

原数据:  [3, 4, 9, 5, 8]
计算数组元素的平方
[9, 16, 81, 25, 64]
计算数组元素的立方
[27, 64, 729, 125, 512]
计算数组元素的阶乘
[6, 24, 362880, 120, 40320]

从上面介绍不难看出，通过使用函数作为参数可以在调用函数时动态传入函数，实际上就可以动态改变被调用函数的部分代码。

在程序最后添加如下一行：

# 获取 map 的类型
print(type(map))

运行上面代码，将会看到如下输出结果：

<class 'function'>

## 使用函数作为返回值

前面己经提到，Python 还支持使用函数作为其他函数的返回值。例如如下程序：

```
def get_math_func(type) :
    # 定义一个计算平方的局部函数
    def square(n) :  # ①
        return n * n
    # 定义一个计算立方的局部函数
    def cube(n) :  # ②
        return n * n * n
    # 定义一个计算阶乘的局部函数
    def factorial(n) :   # ③
        result = 1
        for index in range(2 , n + 1):
            result *= index
        return result
    # 返回局部函数
    if type == "square" :
        return square
    if type == "cube" :
        return cube
    else:
        return factorial
# 调用 get_math_func()，程序返回一个嵌套函数
math_func = get_math_func("cube") # 得到 cube 函数
print(math_func(5)) # 输出 125
math_func = get_math_func("square") # 得到 square 函数
print(math_func(5)) # 输出 25
math_func = get_math_func("other") # 得到 factorial 函数
print(math_func(5)) # 输出 120
```

程序中，定义了一个 get_math_func() 函数，该函数将返回另一个函数。接下来在 get_math_func() 函数体内的 ①、②、③ 号粗体字代码分别定义了三个局部函数，最后 get_math_func() 函数会根据所传入的参数，使用这三个局部函数之一作为返回值。

在定义了会返回函数的 get_math_func() 函数之后，接下来程序调用 get_math_func() 函数时即可返回所需的函数，如上面程序中代码所示。