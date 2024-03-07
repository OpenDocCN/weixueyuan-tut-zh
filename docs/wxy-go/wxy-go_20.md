# Go 语言字符串类型

> 原文：[`www.weixueyuan.net/a/470.html`](http://www.weixueyuan.net/a/470.html)

与 C++ 不同，在 C++ 中，字符串是以类的方式进行封装的，不属于基本数据类型。而在 Go 语言中字符串是以基本数据类型出现的，使用字符串就像使用其他原生基本数据类型 int、float32、float64、bool 一样，如下所示：

var str string                 // 定义名为 str 的字符串类型变量
str = "Hello World！"   // 将变量赋值
name := "微学苑"          // 以自动推断方式初始化一个变量

有些字符串没有现成的文字代号，所以只能用转义字符来表示。常用的转义字符如下所示。

表：转义字符

| 转义字符 | 含义 |
| \r | 回车符 return，返回行首 |
| \n | 换行符 new line，直接跳到下一行的同列位置 |
| \t | 制表符 TAB |
| \' | 单引号 |
| \" | 双引号 |
| \\ | 反斜杠 |

定义多行字符串的方法如下：

*   双引号书写字符串被称为字符串字面量（string literal），这种字面量不能跨行；
*   多行字符串需要使用反引号```，多用于内嵌源码和内嵌数据；
*   在反引号中的所有代码不会被编译器识别，而只是作为字符串的一部分。

【示例】使用反引号```定义多行字符串：

```

package main

import "fmt"

func main() {
    var temp string
    temp = `
        x := 10
        y := 20
        z := 30
        fmt.Println(x, "  ", y, "  ", z)
        x, y, z = y, z, x
        fmt.Println(x, "  ", y, "  ", z)
    `
    fmt.Println(temp)
}
```

运行结果如下：

x := 10
y := 20
z := 30
fmt.Println(x, "  ", y, "  ", z)
x, y, z = y, z, x
fmt.Println(x, "  ", y, "  ", z)