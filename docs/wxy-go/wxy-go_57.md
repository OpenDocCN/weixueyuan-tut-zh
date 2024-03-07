# Go 语言字符串查找

> 原文：[`www.weixueyuan.net/a/576.html`](http://www.weixueyuan.net/a/576.html)

Go 语言中常用的字符串查找方法如下表所示。

表：字符串查找函数

| 方法 | 功能描述 |
| func Contains(s, substr string) bool | 判断字符串 s 是否包含 substr 字符串 |
| func ContainsAny(s, chars string) bool | 判断字符串 s 是否包含 chars 字符串中的任一字符 |
| func ContainsRune(s string, r rune) bool | 判断字符串 s 是否包含 unicode 码值 r |
| func Count(s, sep string) int | 返回字符串 s 包含字符串 sep 的个数 |
| func HasPrefix(s, prefix string) bool | 判断字符串 s 是否有前缀字符串 prefix |
| func HasSuffix(s, suffix string) bool | 判断字符串 s 是否有后缀字符串 suffix |
| func Index(s, sep string) int | 返回字符串 s 中字符串 sep 首次出现的位置 |
| func IndexAny(s, chars string) int  | 返回字符串 chars 中的任意一个 unicode 码值在 s 中首次出现的位置 |
| func IndexByte(s string, c byte) int | 返回字符串 s 中字符 c 首次出现的位置 |
| func IndexFunc(s string, f func(rune) bool) int | 返回字符串 s 中满足函数 f(r)==true 字符首次出现的位置 |
| func IndexRune(s string, r rune) int | 返回 unicode 码值 r 在字符串中首次出现的位置 |
| func LastIndex(s, sep string) int | 返回字符串 s 中字符串 sep 最后一次出现的位置 |
| func LastIndexAny(s, chars string) int | 返回字符串 chars 中的任意一个 unicode 码值在 s 中最后一次出现的位置 |
| func LastIndexByte(s string, c byte) int | 返回字符串 s 中字符 c 最后一次出现的位置 |
| func LastIndexFunc(s string, f func(rune) bool) int | 返回字符串 s 中满足函数 f(r)==true 字符最后一次出现的位置 |

接下来通过一个案例演示部分查找字符串处理函数的使用，代码如下所示。

```

package main
import(
    "fmt"
    "strings"
    "unicode"
)
func main(){
    TestContains()
    TestCount()
    TestIndex()
    TestIndexFunc()
    TestLastIndex()
    TestLastIndexFunc()
    res := GetFileSuffix("abc.xyz.1mn.jpg")
    fmt.Println(res)
}
//判断是否包含子串
func TestContains(){
    fmt.Println(strings.Contains("seafood", "foo"))
    fmt.Println(strings.Contains("seafood", "bar"))
    fmt.Println(strings.Contains("seafood", ""))
    fmt.Println(strings.Contains("", ""))
    fmt.Println(strings.Contains("steven 王 2008", "王"))
}
//判断字符串是否包含另一字符串中的任一字符
func TestContainsAny(){
    fmt.Println(strings.ContainsAny("team", "i"))
    fmt.Println(strings.ContainsAny("failure", "u & i"))
    fmt.Println(strings.ContainsAny("foo", ""))
    fmt.Println(strings.ContainsAny("", ""))
}
//判断字符串是否包含 unicode 码值
func TestContainsRune(){
    fmt.Println(strings.ContainsRune("一丁", '丁'))
    fmt.Println(strings.ContainsRune("一丁", 19969))
}
//返回字符串包含另一字符串的个数
func TestCount(){
    fmt.Println(strings.Count("cheese", "e"))
    fmt.Println(strings.Count("one", ""))
}
//判断字符串 s 是否有前缀字符串
func TestHasPrefix(){
    fmt.Println(strings.HasPrefix("1000phone news", "1000"))
    fmt.Println(strings.HasPrefix("1000phone news", "1000a"))
}
//判断字符串是否有后缀字符串
func TestHasSuffix(){
    fmt.Println(strings.HasSuffix("1000phone news", "news"))
    fmt.Println(strings.HasSuffix("1000phone news", "new"))
}
//返回字符串中另一字符串首次出现的位置
func TestIndex(){
    fmt.Println(strings.Index("chicken", "ken"))
    fmt.Println(strings.Index("chicken", "dmr"))
}
//返回字符串中的任一 unicode 码值首次出现的位置
func TestIndexAny(){
    fmt.Println(strings.IndexAny("abcABC120", "教育基地 A"))
}
//返回字符串中字符首次出现位置
func TestIndexByte(){
    fmt.Println(strings.IndexByte("123abc", 'a'))
}
//判断字符串是否包含 unicode 码值
func TestIndexRune(){
    fmt.Println(strings.IndexRune("abcABC120", 'C'))
    fmt.Println(strings.IndexRune("It 培训教育", '教'))
}
//返回字符串中满足函数 f(r)==true 字符首次出现的位置
func TestIndexFunc(){
    f := func(c rune) bool{
        return unicode.Is(unicode.Han, c)
    }
    fmt.Println(strings.IndexFunc("Hello123, 中国", f))
}
//返回字符串中子串最后一次出现的位置
func TestLastIndex(){
    fmt.Println(strings.LastIndex("Steven learn english", "e"))
    fmt.Println(strings.Index("go gopher", "go"))
    fmt.Println(strings.LastIndex("go gopher", "go"))
    fmt.Println(strings.LastIndex("go gopher", "rodent"))
}
//返回字符串中任意一个 unicode 码值最后一次出现的位置
func TestLastIndexAny(){
    fmt.Println(strings.LastIndexAny("chicken", "aeiouy"))
    fmt.Println(strings.LastIndexAny("crwth", "aeiouy"))
}
//返回字符串中字符最后一次出现的位置
func TestLastIndexByte(){
    fmt.Println(strings.LastIndexByte("abcABCA123", 'A'))
}
//返回字符串中满足函数 f(r)==true 字符最后一次出现的位置
func TestLastIndexFunc(){
    f := func(c rune) bool{
        return unicode.Is(unicode.Han, c)
    }
    fmt.Println(strings.LastIndexFunc("Hello, 世界", f))
    fmt.Println(strings.LastIndexFunc("Hello, world 中国人", f))
}
//获取文件后缀
func GetFileSuffix(str string) string{
    arr := strings.Split(str, ".")
    return arr[len(arr)-1]
}
```

运行结果如下：

true
false
true
true
true
3
4
4
-1
10
13
0
3
-1
10
18
jpg