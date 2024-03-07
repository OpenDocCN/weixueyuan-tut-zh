# Go 语言字符串比较

> 原文：[`www.weixueyuan.net/a/584.html`](http://www.weixueyuan.net/a/584.html)

Go 语言中常用的字符串比较函数如下表所示。

表：字符串比较

| 方法 | 功能描述 |
| func Compare(a, b string) int | 按字典顺序比较 a 和 b 字符串大小 |
| func EqualFold(s, t string) bool | 判断 s 和 t 两个 UTF-8 字符串是否相等，忽略大小写 |
| func Repeat(s string, count int) string | 将字符串 s 重复 count 次返回 |
| func Replace(s, old, new string, n int) string | 替换字符串 s 中 old 字符为 new 字符并返回，n<0 时替换所有 old 字符串 |
| func Join(a []string, sep string) string | 将 a 中的所有字符连接成一个字符串，使用字符串 sep 作为分隔符 |

接下来通过一个示例演示一下部分字符串比较函数的使用，代码如下所示。

```

package main
import(
    "fmt"
    "strings"
)
func main(){
    TestCompare()
    TestEqualFold()
    TestRepeat()
    TestReplace()
    TestJoin()
}
//按字典顺序比较 a 和 b 字符串大小
func TestCompare(){
    fmt.Println(strings.Compare("abc", "bcd"))
    fmt.Println("abs" < "bcd")
}
//判断 s 和 t 两个 UTF-8 字符串是否相等, 忽略大小写
func TestEqualFold(){
    fmt.Println(strings.EqualFold("Go", "go"))
}
//将字符串 s 重复 count 次返回
func TestRepeat(){
    fmt.Println("g" + strings.Repeat("o", 8)+"le")
}
//替换字符串 s 中 old 字符为 new 字符并返回, n<o 时替换所有 old 字符串
func TestReplace(){
    fmt.Println(strings.Replace("王老大 王老二 王老三", "王", "张", 2))
    fmt.Println(strings.Replace("王老大 王老二 王老三", "王", "张", -1))
}
//将 a 中的所有字符连接成一个字符串, 使用字符串 sep 作为分隔符
func TestJoin(){
    s := []string{"abc", "ABC", "123"}
    fmt.Println(strings.Join(s, ", "))
    fmt.Println(strings.Join(s, ""))
}
```

运行结果如下：

-1
true
true
goooooooole
张老大 张老二 王老三
张老大 张老二 张老三
abc, ABC, 123
abcABC123