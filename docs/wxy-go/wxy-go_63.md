# Go 语言数字转字符串

> 原文：[`www.weixueyuan.net/a/589.html`](http://www.weixueyuan.net/a/589.html)

Go 语言的 Format 类函数的主要功能是将其他类型格式化成字符串，常用的 Format 类函数如下表所示。

表：Format 类函数

| 方法  | 功能描述 |
| func Itoa(i int) string | Itoa 是 FormatInt(int64(i), 10) 的缩写，int 转换成 string |
| func FormatInt(i int64, base int) string | FormatInt 返回给定基数的 i 的字符串表示，2 <= base <= 36。结果对于数字值 >=10 使用小写字母 a 到 z |
| func FormatUint(i uint64, base int) string | FormatUint 返回给定基数的 i 的字符串表示，用于无符号数字，2 <= base <= 36。结果对于数字值 >=10 使用小写字母 a 到 z |
| func FormatFloat(f float64, fmt byte, prec, bitSize int) string | 函数将浮点数表示为字符串并返回。bitSize 表示 f 的来源类型（32:float32、64:float64），会据此进行舍入。fmt 表示格式：'f'（-ddd dddd）、"b"（-ddddp±ddd，二进制指数）、'e'（-d.dddde±dd.十进制指数）、'E'（-d.ddddE±dd，十进制指数）、'g'（指数很大时用 'e' 格式，否则 'f' 格式）、'G'（指数很大时用 'E' 格式，否则 'f' 格式）。prec 控制精度（排除指数部分）：对 'f'、'e'、'E'，它表示小数点后的数字个数；对 'g'、'G'，它控制总的数字个数。如果 prec 为 -1，则代表使用最少数量的、但又必需的数字来表示 f |
| func FormatBool(b bool) string | FormatBool 根据 b 的值返回 true 或 false |

接下来通过一个示例来演示一下 Format 类函数的使用，代码如下所示。

```

package main
import(
    "fmt"
    "strconv"
)
func main(){
    TestItoa()
    TestFormatInt()
    TestFormatUint()
    TestFormatFloat()
}
//Int 转换成 string
func TestItoa(){
    s := strconv.Itoa(199)
    fmt.Printf("%T, %v, 长度: %d \n", s, s, len(s))
    fmt.Println("------------------")
}
//返回给定基数的 i 的字符串表示
func TestFormatInt(){
    s := strconv.FormatInt(-19968, 16)
    s = strconv.FormatInt(-40869, 16)
    fmt.Printf("%T, %v, 长度: %d\n", s, s, len(s))
    fmt.Println("------------------")
}
//返回给定基数的 i 的字符串表示
func TestFormatUint(){
    s := strconv.FormatUint(19968, 16)
    s = strconv.FormatUint(40869, 16)
    fmt.Printf("%T, %v, 长度: %d\n", s, s, len(s))
    fmt.Println("------------------")
}
//将浮点数转换为字符串
func TestFormatFloat(){
    s := strconv.FormatFloat(3.1415926, 'g', -1, 64)
    fmt.Printf("%T, %v, 长度: %d\n", s, s, len(s))
    fmt.Println("------------------")
}
```

运行结果如下：

string, 199, 长度: 3
------------------
string, -9fa5, 长度: 5
------------------
string, 9fa5, 长度: 4
------------------
string, 3.1415926, 长度: 9
------------------