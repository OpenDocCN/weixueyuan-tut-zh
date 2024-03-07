# Go 语言格式化输出

> 原文：[`www.weixueyuan.net/a/475.html`](http://www.weixueyuan.net/a/475.html)

在 Go 语言中格式化输出通常使用 fmt 包，通用的输出格式如下表所示：

表：通用输出格式

| 输出格式 | 输出内容 |
| %v | 值的默认格式表示 |
| %+v | 类似 %v，但输出结构体时会添加字段名 |
| %#v | 值的 Go 语法表示 |
| %Т | 值的类型的 Go 语法表示 |

具体的使用方法，如下所示：

```

package main

import "fmt"

func main() {
    str := "steven"
    fmt.Printf("%T, %v \n", str, str)
    var a rune = '一'
    fmt.Printf("%T, %v \n", a, a)
    var b byte = 'b'
    fmt.Printf("%T, %v \n", b, b)
    var c int32 = 98
    fmt.Printf("%T, %v \n", c, c)
}
```

运行结果如下：

string, steven
int32, 19968
uint8, 98
int32, 98

通过上面的示例可以看出，使用通用的格式化输出，输出的结果可能不是自己想要的，为了确保输出结果与需求一致，还需要学习具体格式的输出方式。

## 格式化输出布尔类型

在 Go 语言中，布尔类型的具体输出格式如下表所示：

表：布尔类型输出格式

| 输出格式 | 输出内容 |
| %t | 单词 true 或 false |

格式化输出布尔类型的具体使用方法，如下所示：

```

package main

import "fmt"

func main() {
    var flag bool
    fmt.Printf("%T, %t \n", flag, flag)
    flag = true
    fmt.Printf("%T, %t \n", flag, flag)
}
```

运行结果如下所示：

bool, false
bool, true

## 格式化输出整数类型

整数类型的具体输出格式如下表所示

表：整型的输出格式

| 输出格式 | 输出内容 |
| %b | 表示为二进制 |
| %c | 该值对应的 unicode 码值 |
| %d | 表示为十进制 |
| %8d | 表示该整型长度是 8，不足 8 则在数值前补空格；如果超出 8，则以实际为准 |
| %08d | 表示该整型长度是 8，不足 8 则在数值前补 0；如果超出 8，则以实际为准 |
| %o | 表示为八进制 |
| %q | 该值对应的单引号括起来的 Go 语言语法字符字面值，必要时会采用安全的转义表示 |
| %x | 表示为十六进制，使用 a~f |
| %X | 表示为十六进制，使用 A~F |
| %U | 表示为 unicode 格式：U+1234，等价于 U+%04X |

具体的使用方法，如下所示

```

package main

import "fmt"

func main() {
    fmt.Printf("%T, %d \n", 123, 123)
    fmt.Printf("%T, %5d \n", 123, 123)
    fmt.Printf("%T, %05d \n", 123, 123)
    fmt.Printf("%T, %b \n", 123, 123)
    fmt.Printf("%T, %o \n", 123, 123)
    fmt.Printf("%T, %c \n", 97, 97)
    fmt.Printf("%T, %q \n", 97, 97)
    fmt.Printf("%T, %x \n", 123, 123)
    fmt.Printf("%T, %X \n", 123, 123)
    fmt.Printf("%T, %U \n", '一', '一')
}
```

运行结果如下：

int, 123
int,   123
int, 00123
int, 1111011
int, 173
int, a
int, 'a'
int, 7b
int, 7B
int32, U+4E00

## 格式化输出浮点型与复数型

浮点型的具体输出格式如下表所示：

表：浮点型输出格式

| 输出格式 | 输出内容 |
| %b | 无小数部分、二进制指数的科学计数法，如 -123456p-78 |
| %e | （=%.6e）有 6 位小数部分的科学计数法，如 -1234.456e+78 |
| %E | 科学计数法，如 -1234.456E+78 |
| %f  | （=%.6f）有 6 位小数部分，如 123.456123 |
| %F | 等价于 %f |
| %g | 根据实际情况采用 %e 或 %f 格式（获得更简洁、准确的输出） |
| %G | 根据实际情况采用 %E 或 %F 格式（获得更简洁、准确的输出） |

具体的使用方法，如下所示：

```

package main

import "fmt"

func main() {
    fmt.Printf("%b \n", 123.123456)
    fmt.Printf("%f \n", 123.1)
    fmt.Printf("%.2f \n", 123.125456)
    fmt.Printf("%e \n", 123.123456)
    fmt.Printf("%E \n", 123.123456)
    fmt.Printf("%.1e \n", 123.123456)
    fmt.Printf("%F \n", 123.123456)
    fmt.Printf("%g \n", 123.123456)
    fmt.Printf("%G \n", 123.123456)
}
```

运行结果如下：

8664042977533870p-46
123.100000
123.13
1.231235e+02
1.231235E+02
1.2e+02
123.123456
123.123456
123.123456

关于复数类型的输出演示，如下所示：

```

package main

import "fmt"

func main() {
    var value complex64 = 2.1 + 12i
    value2 := complex(2.1, 12)
    fmt.Println(real(value))
    fmt.Println(imag(value))
    fmt.Println(value2)
}
```

运行结果如下：

2.1
12
(2.1+12i)

## 格式化输出字符串与字节数组

字符串和字节数组的具体输出格式如下表所示：

表：字符串输出格式

| 输出格式 | 输出内容 |
| %s | 直接输出字符串或者字节数组 |
| %q | 该值对应的双引号括起来的 Go 语法字符串字面值，必要时会采用安全的转义表示 |
| %x | 每个字节用两字符十六进制数表示，使用 a~f |
| %X | 每个字节用两字符十六进制数表示，使用 A~F |

具体的使用方法，如下所示：

```

package main

import "fmt"

func main() {
    arr := []byte{'x', 'y', 'z', 'z'}
    fmt.Printf("%s \n", "欢迎访问微学苑")
    fmt.Printf("%q \n", "欢迎访问微学苑")
    fmt.Printf("%x \n", "欢迎访问微学苑")
    fmt.Printf("%X \n", "欢迎访问微学苑")
    fmt.Printf("%T, %s \n", arr, arr)
    fmt.Printf("%T, %q \n", arr, arr)
    fmt.Printf("%T, %x \n", arr, arr)
    fmt.Printf("%T, %X \n", arr, arr)
}
```

运行结果如下：

欢迎访问微学苑
"欢迎访问微学苑"
e6aca2e8bf8ee8aebfe997aee5beaee5ada6e88b91
E6ACA2E8BF8EE8AEBFE997AEE5BEAEE5ADA6E88B91
[]uint8, xyzz
[]uint8, "xyzz"
[]uint8, 78797a7a
[]uint8, 78797A7A