# Go 语言赋值运算符

> 原文：[`www.weixueyuan.net/a/498.html`](http://www.weixueyuan.net/a/498.html)

Go 语言的赋值运算符如下所示。

表：赋值运算符

| 运算符 | 描述 | 实例 |
| = | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C=A+B 将 A+B 表达式结果赋值给 C |
| +=  | 相加后再赋值 | C+=A 等于 C=C+A |
| -= | 相减后再赋值 | C-=A 等于 C=C-A |
| *= | 相乘后再赋值 | C*=A 等于 C=C*A |
| /= | 相除后再赋值 | C/=A 等于 C=C/A |
| %= | 求余后再赋值   | C%=A 等于 C=C%A |
| <<= | 左移后赋值 | C<<=2 等于 C=C<<2 |
| >>= | 右移后赋值 | C>>=2 等于 C=C>>2 |
| &= | 按位与后赋值   | C&=2 等于 C=C&2 |
| ^= | 按位异或后赋值 | C^=2 等于 C=C² |
| &#124;= | 按位或后赋值 | C&#124;=2 等于 C=C&#124;2 |

赋值运算符的用法，如下所示。

```

package main

import "fmt"

func main() {
    var a int = 21
    var c int
    c = a
    fmt.Printf("第 1 行 = 运算符实例，c 的值为：%d\n", c)
    c += a
    fmt.Printf("第 2 行 += 运算符实例，c 的值为：%d\n", c)
    c -= a
    fmt.Printf("第 3 行 -= 运算符实例，c 的值为：%d\n", c)
    c *= a
    fmt.Printf("第 4 行 *= 运算符实例，c 的值为：%d\n", c)
    c /= a
    fmt.Printf("第 5 行 /= 运算符实例，c 的值为：%d\n", c)
    c = 200
    c <<= 2
    fmt.Printf("第 6 行 <<= 运算符实例，c 的值为：%d\n", c)
    c >>= a
    fmt.Printf("第 7 行 >>= 运算符实例，c 的值为：%d\n", c)
    c &= a
    fmt.Printf("第 8 行 &= 运算符实例，c 的值为：%d\n", c)
    c ^= a
    fmt.Printf("第 9 行 ^= 运算符实例，c 的值为：%d\n", c)
    c |= a
    fmt.Printf("第 10 行 |= 运算符实例，c 的值为：%d\n", c)
}
```

运行结果如下：

第 1 行 = 运算符实例，c 的值为：21
第 2 行 += 运算符实例，c 的值为：42
第 3 行 -= 运算符实例，c 的值为：21
第 4 行 *= 运算符实例，c 的值为：441
第 5 行 /= 运算符实例，c 的值为：21
第 6 行 <<= 运算符实例，c 的值为：800
第 7 行 >>= 运算符实例，c 的值为：0
第 8 行 &= 运算符实例，c 的值为：0
第 9 行 ^= 运算符实例，c 的值为：21
第 10 行 |= 运算符实例，c 的值为：21

除了前面介绍的运算符外，Go 语言中还有一些其它的运算符，如下表所示：

表：其它运算符

| 运算符 | 描述 | 实例 |
| & | 返回变量存储地址 | &a 将给出变量的实际地址 |
| * | 指针变量 | *a 是一个指针变量 |

上表中运算符的使用方法如下所示。

```

package main

import "fmt"

func main() {
    var a int = 4
    var b int32
    var c float32
    var ptr *int
    fmt.Printf("第 1 行 a 变量类型为：%T\n", a)
    fmt.Printf("第 2 行 b 变量类型为：%T\n", b)
    fmt.Printf("第 3 行 c 变量类型为：%T\n", c)
    //& 和 * 运算符实例
    ptr = &a // ptr 包含了 a 变量的地址
    fmt.Printf("a 的值为：%d\n", a)
    fmt.Printf("*ptr 为：%d\n", *ptr)
}
```

运行结果如下：

第 1 行 a 变量类型为：int
第 2 行 b 变量类型为：int32
第 3 行 c 变量类型为：float32
a 的值为：4
*ptr 为：4