# Python 浮点数（小数）

> 原文：[`www.weixueyuan.net/a/386.html`](http://www.weixueyuan.net/a/386.html)

浮点数简单来说就是实数，也叫小数，不同于整数，其带有小数部分。浮点数广泛用于统计和科学计算上。

现实生活中，我们经常说的一个半小时，其应该用 1.5 个小时来表示。所有浮点数自然存在于生活中，并且广泛存在生活中。

所有的浮点数都是近似的。

```

>>> 1.2 * 6
7.199999999999999
>>> 1.2 * 6 == 7.2  # 它们居然不相等
False
```

所以比较两个浮点数是否相等需要用 abs(a-b)<1e-5 来判断。

```

>>> a = 1.2 * 6
>>> abs(a-7.2) < 1e-5
True
```

至于为何浮点数不同于整数，其值可以认为都是近似值呢？这与采用的进制有关。如在十进制中，三分之一（1/3）就是无法准确表示的，只能近似等于 0.333333。但是这个数在三进制中就可以准确表示为 0.1。同样的情况也发生在二进制中，如十进制中的 0.1 在二进制中就不能准确表示。

下面以浮点数 10.5 为例介绍浮点数的表示法。浮点数一般分为两部分，整数部分和小数部分。整数部分的表示法和整数的二进制表示法相同，如 10 用二进制数表示就是 1010。

10 = 8+2 = 2³+2¹ = 0b1010

小数部分每位的权重依次是 2^(−1)、2^(−2)、2^(−3)、2^(−4)，所以 0.5 就可以表示为 0.1。

所以 10.5 用二进制数表示为 0b1010.1 = 0b1.0101*2³。这就是标准的浮点数表示法，其包括数值部分 0b1.0101 和指数 3。数值部分大于或等于 1 而且小于 2。

如果用十六进制表示就是：

0b1.0101*2³ =0x1.5*2³

但是我们也不需要每个数都这么来手工计算，Python 提供了函数 hex()，它是浮点数对象的成员函数。下面演示其用法。

```

>>> 10.5.hex()          # 得到其内部表示法
'0x1.5000000000000p+3'  # 数值部分是 0x1.5，指数部分是 3
```

## 浮点数的基本运算

本节继续介绍浮点数的基本运算，包括加减乘除和幂运算。

#### 1) 加减乘除运算（+-*/）

和整数一样，浮点数也支持加减乘除等基本运算，运算符号也相同。

```

>>> 1.2 + 1.3        # 加法运算
2.5
>>> 1.2 - 2.3        # 减法运算
-1.0999999999999999  # 得到一个近似值
>>> 1.2 * 2.0        # 乘法运算
2.4
>>> 1.2 / 2.0        # 除法运算
0.6
```

#### 2) 幂运算（**）

浮点数也支持幂运算，符号也是 **。但是负数不支持小数幂的运算。

```

>>> 1.2 ** 2
1.44
>>> 1.2 ** 2.3
1.5209567545525315
>>> -1.2 ** 2                       # 等效于-(1.2 ** 2)
-1.44
>>> (-1.2) ** 2                     # 负数的平方为正数
1.44
>>> (-1.2) ** 2.1                   # 负数不支持小数幂运算
Traceback (most recent call last):  # 抛出异常
  File "<stdin>", line 1, in <module>
    ValueError: negative number cannot be raised to a fractional power
```

## 浮点数的其他运算

1) 将浮点数转换成近似的两个整数的商——as_integer_ratio()

这是一个浮点数的成员函数。其用法如下：

```

>>> a = 0.1                                 # 操作数
>>> a.as_integer_ratio()
(3602879701896397, 36028797018963968)
>>> 3602879701896397.0 / 36028797018963968  # 两者的值很接近了
0.1
```

2) 判断某个浮点数是否为整数——is_integer()

这是浮点数对象自带的函数，判断某个浮点数是否为一个整数。其用法如下：

```

>>> a = 12.0
>>> a.is_integer()
True
>>> a = 12.0001
>>> a.is_integer()
False
```

当然这也是一个近似值，如 1.0000000000000001 就被看做 1。下面演示了这种情况。

```

>>> 1.0000000000000001.is_integer()  # 判断是否可以当作整数使用
True
>>> 1.0000000000000001 == 1.0
True
```