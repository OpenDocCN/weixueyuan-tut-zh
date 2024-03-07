# Go 语言算术运算符

> 原文：[`www.weixueyuan.net/a/485.html`](http://www.weixueyuan.net/a/485.html)

运算符用于在程序运行时执行数学或逻辑运算。Go 语言中内置的运算符包括算术运算符、关系运算符、逻辑运算符、位运算符、赋值运算符、其他运算符。

本节主要来介绍一下 Go 语言中的算术运算符，如下表所示。（假定 A 值为 10，B 值为 20）

表：Go 语言中的算术运算符

| 运算符 | 描述 | 实例 |
| + | 相加 | A+B 输出结果 30 |
| - | 相减 | A-B 输出结果 -10 |
| * | 相乘 | A*B 输出结果 200 |
| / | 相除  | B/A 输出结果 2 |
| % | 求余 | B%A 输出结果 0 |
| ++ | 自增 | A++ 输出结果 11 |
| -- | 自减 | A-- 输出结果 9 |

算术运算符的用法如下所示。

```

package main

import "fmt"

func main() {
    var a int = 21
    var b int = 10
    var c int
    c = a + b
    fmt.Printf("第一行 -c 的值为 %d\n", c)
    c = a - b
    fmt.Printf("第二行 -c 的值为 %d\n", c)
    c = a * b
    fmt.Printf("第三行 -c 的值为 %d\n", c)
    c = a / b
    fmt.Printf("第四行 -c 的值为 %d\n", c)
    c = a % b
    fmt.Printf("第五行 -c 的值为 %d\n", c)
    a++
    fmt.Printf("第六行 -c 的值为 %d\n", a)
    a = 21 // 为了方便测试，这里将 a 重新赋值为 21
    a--
    fmt.Printf("第七行 -a 的值为 %d\n", a)
}
```

运行结果如下：

第一行 -c 的值为 31
第二行 -c 的值为 11
第三行 -c 的值为 210
第四行 -c 的值为 2
第五行 -c 的值为 1
第六行 -c 的值为 22
第七行 -a 的值为 20