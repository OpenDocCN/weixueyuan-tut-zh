# Go 语言去除字符串中的指定字符

> 原文：[`www.weixueyuan.net/a/582.html`](http://www.weixueyuan.net/a/582.html)

Go 语言中常用的去除字符串中指定字符的方法如下表所示。

表：字符串处理函数

| 方法 | 功能描述 |
| func Trim(s string, cutset string) string | 将字符串 s 首尾包含在 cutset 中的任一字符去掉返回 |
| func TrimFunc(s string, f func(rune) bool) string | 将字符串 s 首尾满足函数 f(r)==true 的字符去掉返回 |
| func TrimLeft(s string, cutset string) string | 将字符串 s 左边包含在 cutset 中的任一字符去掉返回 |
| func TrimLeftFunc(s string, f func(rune) bool) string | 将字符串 s 左边满足函数 f(r)==true 的字符去掉返回 |
| func TrimPrefix(s, prefix string) string | 将字符串 s 中前缀字符串 prefix 去掉返回 |
| func TrimRight(s string, cutset string) string | 将字符串 s 右边包含在 cutset 中的任一字符去掉返回 |
| fune TrimRightFunc(s string, f func(rune) bool) string | 将字符串 s 右边满足函数 f(r)==true 的字符去掉返回 |
| func TrimSpace(s string) string | 将字符串 s 首尾空白去掉返回 |
| func TrimSuffix(s, suffix string) string | 将字符串 s 中后缀字符串 suffix 去掉返回 |

接下来通过一个示例来演示部分字符串处理函数的使用，代码如下所示。

```

package main
import(
    "fmt"
    "strings"
    "unicode"
)
func main(){
    TestTrim()
    TestTrimFunc()
    TestTrimLeft()
    TestTrimLeftFunc()
    TestTrimRight()
    TestTrimRightFunc()
    TestTrimSpace()
    TestTrimPrefix()
    TestTrimSuffix()
}
//将字符串 s 首尾包含在 cutset 中的任一字符去掉返回
func TestTrim(){
    fmt.Println(strings.Trim("  steven wang  ", " "))
}
//将字符串 s 首尾满足函数 f(r)==true 的字符去掉返回
func TestTrimFunc(){
    f := func(c rune)bool {
        return !unicode.IsLetter(c) && !unicode.IsNumber(c)
    }
    fmt.Println(strings.TrimFunc("! @#￥ %steven wang%$#@", f))
}
//将字符串 s 左边包含在 cutset 中的任一字符去掉返回
func TestTrimLeft(){
    fmt.Println(strings.TrimLeft("   steven wang   ", " "))
}
//将字符串 s 左边满足函数 f(r)==true 的字符去掉返回
func TestTrimLeftFunc(){
    f := func(c rune)bool{
        return !unicode.IsLetter(c) && !unicode.IsNumber(c)
    }
    fmt.Println(strings.TrimLeftFunc("！@#￥%steven wang%￥#@", f))
}
//将字符串 s 右边包含在 cutset 中的任一字符去掉返回
func TestTrimRight(){
    fmt.Println(strings.TrimRight("    steven wang    ", " "))
}
//将字符串 s 右边满足函数 f(r)==true 的字符去掉返回
func TestTrimRightFunc(){
    f := func(c rune)bool{
        return !unicode.IsLetter(c) && !unicode.IsNumber(c)
    }
    fmt.Println(strings.TrimRightFunc("！@#￥%steven wang%￥#@", f))
}
//将字符串 s 首尾空白去掉返回
func TestTrimSpace(){
    fmt.Println(strings.TrimSpace(" \t\n a lone gopher \n\t\r\n"))
}
//将字符串 s 中前缀字符串 prefix 去掉返回
func TestTrimPrefix(){
    var s = "Goodbye, world！"
    s = strings.TrimPrefix(s, "Goodbye")//, world！
    fmt.Println(s)
}
//将字符串 s 中后缀字符串 suffix 去掉返回
func TestTrimSuffix(){
    var s = "Hello, goodbye, etc！"
    s = strings.TrimSuffix(s, "goodbye, etc！")//Hello,
    fmt.Println(s)
}
```

运行结果如下：

steven wang
steven wang
steven wang  
steven wang%￥#@
    steven wang
！@#￥%steven wang
a lone gopher
, world！
Hello,