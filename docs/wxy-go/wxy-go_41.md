# Go 语言匿名函数

> 原文：[`www.weixueyuan.net/a/530.html`](http://www.weixueyuan.net/a/530.html)

Go 语言支持匿名函数，即在需要使用函数时再定义函数。匿名函数没有函数名，只有函数体，函数可以作为一种类型被赋值给变量，匿名函数也往往以变量方式被传递。

匿名函数经常被用于实现回调函数、闭包等。语法格式如下所示。

func(参数列表) (返回参数列表){
    // 函数体
}

#### 1) 在定义时调用匿名函数

【示例】匿名函数使用方式一。

```

package main

import "fmt"

func main() {
    func(data int) {
        fmt.Println("hello", data)
    }(100)
}
```

运行结果如下：

hello 100

#### 2) 将匿名函数赋值给变量

【示例】匿名函数使用方式二。

```

package main

import "fmt"

func main() {
    f := func(data string) {
        fmt.Println(data)
    }
    f("欢迎访问微学苑")
}
```

运行结果如下：

欢迎访问微学苑

#### 3) 匿名函数用作回调函数

【示例】匿名函数使用方式三。

```

package main
import (
    "fmt"
    "math"
)
func main() {
    // 调用函数，对每个元素进行求平方根操作
    arr := []float64{1, 9, 16, 25, 30}
    visit(arr, func(v float64) {
        v = math.Sqrt(v)
        fmt.Printf("%.2f \n", v)
    })
    // 调用函数，对每个元素进行求平方根操作
    visit(arr, func(v float64) {
        v = math.Pow(v, 2)
        fmt.Printf("%.0f \n", v)
    })
}
// 定义一个函数，遍历切片元素，对每个元素进行处理
func visit(list []float64, f func(float64)) {
    for _, value := range list {
        f(value)
    }
}
```

运行结果如下：

1.00
3.00
4.00
5.00
5.48
1
81
256
625
900

第 20 行定义了函数 visit()，第二个参数为函数变量。第 9 行调用了 visit() 函数，第二个参数传入了匿名函数，后面的大括号实现了匿名函数的功能。