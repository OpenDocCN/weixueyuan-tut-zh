# Go 语言运算符优先级

> 原文：[`www.weixueyuan.net/a/500.html`](http://www.weixueyuan.net/a/500.html)

有些运算符拥有较高的优先级，二元运算符的运算方向均是从左至右。所有运算符以及它们的优先级如下表所示。

表：运算符优先级

| 优先级 | 运算符 |
| 7 | ^ ! |
| 6 | * / % << >> & &^ |
| 5 | + - &#124; ^ |
| 4 | == != < <= >= > |
| 3 | <- |
| 2 | && |
| 1 | &#124;&#124; |

当然，读者可以通过使用括号来临时提升某个表达式的整体运算优先级。运算符优先级的用法，如下所示。

```

package main

import "fmt"

func main() {
    var a int = 20
    var b int = 10
    var c int = 15
    var d int = 5
    var e int
    e = (a + b) * c / d
    fmt.Printf("(a + b) * c / d 的值为：%d\n", e)
    e = ((a + b) * c) / d
    fmt.Printf("((a + b) * c) / d 的值为：%d\n", e)
    e = (a + b) * (c / d)
    fmt.Printf("(a + b) * (c / d) 的值为：%d\n", e)
    e = a + (b * c)/d
    fmt.Printf("a + (b * c) / d 的值为：%d\n", e)
}
```

运行结果如下：

(a + b) * c / d 的值为：90
((a + b) * c) / d 的值为：90
(a + b) * (c / d) 的值为：90
a + (b * c) / d 的值为：50