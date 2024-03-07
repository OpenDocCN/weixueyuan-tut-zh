# Go 语言逻辑运算符

> 原文：[`www.weixueyuan.net/a/488.html`](http://www.weixueyuan.net/a/488.html)

Go 语言的逻辑运算符如下表所示。（假定 A 值为 True，B 值为 False）

表：逻辑运算符

| 运算符 | 描述 | 实例 |
| && | 逻辑 AND 运算符。如果两边的操作数都是 True，则条件 True，否则为 False | (A && B) 为 False |
| &#124;&#124; | 逻辑 OR 运算符。如果两边的操作数有一个 True，则条件 True，否则为 False | (A &#124;&#124; B) 为 True |
| !  | 逻辑 NOT 运算符。如果条件为 True，则逻辑 NOT 条件 False，否则为 True | !(A && B) 为 True |

逻辑运算符的用法如下所示。

```

package main

import "fmt"

func main() {
    var a bool = true
    var b bool = false
    if a && b {
        fmt.Printf("第一行 - 条件为 true \n")
    }
    if a || b {
        fmt.Printf("第二行 - 条件为 true \n")
    }
    // 修改 a 和 b 的值
    a = false
    b = true
    if a && b {
        fmt.Printf("第三行 - 条件为 true \n")
    } else {
        fmt.Printf("第三行 - 条件为 false \n")
    }
    if !(a && b) {
        fmt.Printf("第四行 - 条件为 true \n")
    }
}
```

运行结果如下：

第二行 - 条件为 true
第三行 - 条件为 false
第四行 - 条件为 true