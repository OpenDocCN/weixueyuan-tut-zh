# Go 语言变量作用域

> 原文：[`www.weixueyuan.net/a/519.html`](http://www.weixueyuan.net/a/519.html)

作用域是变量、常量、类型、函数的作用范围。

在函数体内声明的变量称为局部变量，它们的作用域只在函数体内，生命周期同所在的函数。参数和返回值变量也是局部变量。

在函数体外声明的变量称为全局变量，全局变量可以在整个包甚至外部包（被导出后）使用。全局变量的生命周期同 main() 函数。

全局变量可以在任何函数中使用。Go 语言程序中全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑。

函数中定义的参数称为形式参数，形式参数会作为函数的局部变量来使用。

为了让大家更直观地理解作用域，下面通过一个示例加以分析，如下所示。

```

package main

import "fmt"

var a1 int = 7
var b1 int = 9

func main() {
    // main 函数中声明局部变量
    a1, b1, c1 := 10, 20, 0
    fmt.Printf("main() 函数中 a1 = %d\n", a1)
    fmt.Printf("main() 函数中 b1 = %d\n", b1)
    fmt.Printf("main() 函数中 c1 = %d\n", c1)
    c1 = sum(a1, b1)
    fmt.Printf("main() 函数中 c1 = %d\n", c1)
}
func sum(a1, b1 int) (c1 int) {
    a1++
    b1 += 2
    c1 = a1 + b1
    fmt.Printf("sum() 函数中 a1 = %d\n", a1)
    fmt.Printf("sum() 函数中 b1 = %d\n", b1)
    fmt.Printf("sum() 函数中 c1 = %d\n", c1)
    return c1
}
```

运行结果如下：

main() 函数中 a1 = 10
main() 函数中 b1 = 20
main() 函数中 c1 = 0
sum() 函数中 a1 = 11
sum() 函数中 b1 = 22
sum() 函数中 c1 = 33
main() 函数中 c1 = 33

由运行结果可以看出，同样变量名的局部变量和全局变量，同一作用域内优先使用局部变量，正所谓“强龙不压地头蛇”。