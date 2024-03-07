# Go 语言浮点类型（小数类型）

Go 语言支持两种浮点型数：float32 和 float64。这两种浮点型数据格式遵循 IEEE 754 标准：

*   float32 的浮点数的最大范围约为 3.4e38，可以使用常量定义：math.MaxFloat32。
*   float64 的浮点数的最大范围约为 1.8e308，可以使用一个常量定义：math.MaxFloat64。

打印浮点数时，可以使用 fmt 包配合动词`%f`，代码如下：

```
package main

import (
        "fmt"
        "math"
)

func main() {
        fmt.Printf("%f\n", math.Pi)
        fmt.Printf("%.2f\n", math.Pi)
}
```

代码说明如下：

*   第 9 行，按默认宽度和精度输出整型。
*   第 10 行，按默认宽度，2 位精度输出（小数点后的位数）。

代码运行结果如下：

3.141593
3.14