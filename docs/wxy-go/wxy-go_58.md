# Go 语言字符串分割

> 原文：[`www.weixueyuan.net/a/578.html`](http://www.weixueyuan.net/a/578.html)

Go 语言中字符串分割的常用方法如下表所示。

表：字符串分割函数

| 方法 | 功能描述 |
| func Fields(s string) []string | 将字符串 s 以空白字符分割，返回一个切片 |
| func FieldsFunc(s string, f func(rune)bool) []string | 将字符串 s 以满足 f(r)==tnue 的字符分割，返回一个切片 |
| func Split(s, sep string) []string | 将字符串 s 以 sep 作为分隔符进行分割，分割后字符最后去掉 sep |
| func SplitAfter(s, sep string) []string | 将字符串 s 以 sep 作为分隔符进行分割，分割后字符最后附上 sep |
| func SplitAfterN(s, sep string, n int) []string | 将字符串 s 以 sep 作为分隔符进行分割，分割后字符最后附上 sep，n 决定返回的切片数 |
| func SplitN(s, sep string, n int) []string | 将字符串 s 以 sep 作为分隔符进行分割，分割后字符最后去掉 sep，n 决定返回的切片数 |

接下来通过一个案例演示部分字符串分割函数，示例代码如下所示。

```

package main
import(
    "fmt"
    "strings"
    "unicode"
)
func main(){
    TestFields()
    TestFieldsFunc()
    TestSplitAfterN()
    TestSplit()
}
//将字符串以空白字符分割, 并返回一个切片
func TestFields(){
    fmt.Println(strings.Fields("abc 123 ABC xyz XYZ"))
}
//将字符串以满足 f(r)==true 的字符分割, 返回一个切片
func TestFieldsFunc(){
    f := func(c rune) bool{
        // return c == '='
        return !unicode.IsLetter(c) && !unicode.IsNumber(c)
    }
    fmt.Println(strings.FieldsFunc("abc@123*ABC&xyz%XYZ", f))
}
//将字符串以 sep 作为分隔符进行分割, 分割后字符最后去掉 sep
func TestSplit(){
    fmt.Printf("%q\n", strings.Split("a, b, c", ", "))
    fmt.Printf("%q\n", strings.Split("a man a plan a canal panama", "a"))
    fmt.Printf("%q\n", strings.Split(" xyz ", ""))
    fmt.Printf("%q\n", strings.Split("", "Bernardo O'Higgins"))
}
//将字符串 s 以 sep 作为分隔符进行分割, 分割后字符最后附上 sep, n 决定返回的切片数
func TestSplitN(){
    fmt.Printf("%q\n", strings.SplitN("a, b, c", ", ", 2))
    fmt.Printf("%q\n", strings.SplitN("a, b, c", ", ", 1))
}
//将字符串 s 以 sep 作为分隔符进行分割, 分割后字符最后附上 sep
func TestSplitAfter(){
    fmt.Printf("%q\n", strings.SplitAfter("a, b, c", ", "))
}
//将字符串 s 以 sep 作为分隔符进行分割, 分割后字符最后附上 sep, n 决定返回的切片数
func TestSplitAfterN(){
    fmt.Printf("%q\n", strings.SplitAfterN("a, b, c", ", ", 2))
}
```

运行结果如下：

[abc 123 ABC xyz XYZ]
[abc 123 ABC xyz XYZ]
["a, " "b, c"]
["a" "b" "c"]
["" " m" "n " " pl" "n " " c" "n" "l p" "n" "m" ""]
[" " "x" "y" "z" " "]
[""]