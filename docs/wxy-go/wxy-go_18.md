# Go 语言复数类型

> 原文：[`www.weixueyuan.net/a/463.html`](http://www.weixueyuan.net/a/463.html)

复数类型用于表示数学中的复数，如 1+2j、1-2j、-1-2j 等。复数实际上由两个实数（在计算机中用浮点数表示）构成，一个表示实部（real），一个表示虚部（imag），如果了解了数学上的复数是怎么回事，那么对于 Go 语言中的复数就非常容易理解了。

Go 语言中有两种复数类型，如下表所示。

表：复数类型

| 类型 | 字节数 | 说明 |
| complex64 | 8 | 64 位的复数型，由 float32 类型的实部和部联合表示 |
| complex128 | 16 | 128 位的复数型，由 float64 类型的实部和虚部联合表示 |

可以通过 Go 语言内置的 complex() 函数构建复数，并通过内置的 real() 和 imag() 函数分别返回复数的实部和虚部，示例代码如下所示：

```

package main

import (
    "fmt"
)

func main() {
    var x complex128 = complex(1, 2) //1+2i
    var y complex128 = complex(3, 4) //3+4i
    fmt.Println(x * y)               //(-5+10i)
    fmt.Println(real(x * y))         //-5
    fmt.Println(imag(x * y))         //10
}
```

运行结果如下：

(-5+10i)
-5
10

复数使用 re+imI 来表示，其中 re 代表实数部分，im 代表虚数部分，I 代表根号负 1。如果一个浮点数面值或一个十进制整数面值后面跟着一个 i，例如 3.141592i 或 2i，它将构成一个复数的虚部，复数的实部是 0：

fmt.Println(1i*1i)   // (-1+0i) ，i²=-1

如果 re 和 im 的类型均为 float32，那么类型为 complex64 的复数 c 可以通过以下方式来获得：

c = complex(re, im)

函数 real(c) 和 imag(c) 可以分别获得相应的实数和虚数部分。

另外，复数支持和其它数字类型一样的运算。当你使用等号 == 或者不等号 != 对复数进行比较运算时，注意对精确度的把握。cmath 包中包含了一些操作复数的公共方法。如果你对内存的要求不是特别高，最好使用 complex128 作为计算类型，因为相关函数都使用这个类型的参数。