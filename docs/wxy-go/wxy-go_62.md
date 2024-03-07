# Go 语言字符串转数字

> 原文：[`www.weixueyuan.net/a/588.html`](http://www.weixueyuan.net/a/588.html)

Go 语言中的 Parse 类函数的主要功能就是将字符串转换为其他数字类型, 常用的 Parse 类函数如下表所示。

表：Parse 类函数

| 方法 | 功能描述 |
| func Atoi(s string)(int, error)  | 将字符串类型转换为整型 |
| func ParseInt(s string, base int, bitSize int)(i int64, err error)  | ParseInt 将字符串解析成数字，base 表示进制（2 到 36）。如果 base 为 0，则会从字符串前缀判断，"0x"表示十六进制，"0"表示八进制，否则就是十进制。bitSize 指定结果必须是能无溢出赋值的整数类型，0、8、16、32、64 分别代表 int、int8、int16、int32、int64 |
| func ParseUint(s string, base int, bitSize int)(uint64, error) | ParseUint 类似 ParseInt，但是用于无符号数字 |
| finc ParseFloat(s string, bitSize int)(float64, error) | ParseFloat 解析一个表示浮点数的字符串并返回其值，如果字符串 s 符合语法规则，函数会返回最为接近 s 值的一个浮点数，精度由 bitSize 指定，float32 的精度为 64，float64 的精度为 64，当 bitSize=32 时，结果仍然可以是 float64 类型，但也可以在不更改其值的情况下将其类型转换为 float32 |
| func ParseBool(str string)(bool, error)  | ParseBool 返回字符串表示的布尔值。它接受 1、t、T、TRUE、true、True、0、f、F、FALSE、false、False。任何其他值都会返回错误 |

接下来通过一个示例来演示一下 Parse 类函数的使用，代码如下所示。

```

package main
import(
    "strconv"
    "fmt"
)
func main(){
    TestAtoi()
    TestParseInt()
    TestParseUint()
    TestParseFloat()
    TestParseBool()
}
//将字符串类型转换为 int 类型
func TestAtoi(){
    a, _ := strconv.Atoi("100")
    fmt.Printf("%T, %v \n", a, a+2)
    fmt.Println("-----------------")
}
//解释给定基数（2 到 36）的字符串 s 并返回相应的值 i
func TestParseInt(){
    num, _ := strconv.ParseInt("-4e00", 16, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseInt("01100001", 2, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseInt("-01100001", 10, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseInt("4e00", 10, 64)
    fmt.Printf("%T, %v \n", num, num)
    fmt.Println("-----------------")
}
//ParseUint 类似 ParseInt, 但是用于无符号数字
func TestParseUint(){
    num, _ := strconv.ParseUint("4e00", 16, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseUint("01100001", 2, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseUint("-1100001", 10, 64)
    fmt.Printf("%T, %v \n", num, num)
    num, _ = strconv.ParseUint("4e00", 10, 64)
    fmt.Printf("%T, %v \n", num, num)
    fmt.Println("-----------------")
}
//ParseFloat 将字符串 s 转换为 float 类型
func TestParseFloat(){
    pi := "3.1415926"
    num, _ := strconv.ParseFloat(pi, 64)
    fmt.Printf("%T, %v\n", num, num*2)
    fmt.Println("-----------------")
}
//将字符串转换为 bool 类型
func TestParseBool(){
    flag, _ := strconv.ParseBool("steven")
    fmt.Printf("%T, %v \n", flag, flag)
    fmt.Println("-----------------")
}
```

运行结果如下：

int, 102
-----------------
int64, -19968
int64, 97
int64, -1100001
int64, 0
-----------------
uint64, 19968
uint64, 97
uint64, 0
uint64, 0
-----------------
float64, 6.2831852
-----------------
bool, false
-----------------