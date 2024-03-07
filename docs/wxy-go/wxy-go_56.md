# Go 语言遍历字符串

> 原文：[`www.weixueyuan.net/a/572.html`](http://www.weixueyuan.net/a/572.html)

一般编程语言包含的字符串处理库功能的区别不是很大，高级的语言提供的函数会更多，掌握基本的字符串处理函数后，更丰富的字符串处理函数都是通过封装基本的处理函数实现。因此熟悉 Go 语言的 strings 包后基本就能借此封装符合自己需求的、应用于特定场景的字符串处理函数了。而 strconv 包实现了字符串与其他基本数据类型之间的类型转换。

字符串的遍历包括按字节遍历和按字符遍历，示例代码如下所示。

```

package main
import(
    "fmt"
    "unicode/utf8"
)
func main(){
    s := "我爱 Go 语言"
    fmt.Println("字节长度", len(s))
    fmt.Println("---------")
    //for...range 遍历字符串
    len := 0
    for i, ch := range s{
        fmt.Printf("%d:%c", i, ch)
        len++
    }
    fmt.Println("\n 字符串长度", len)
    fmt.Println("------------")
    //遍历所有字节
    for i, ch := range []byte(s){
        fmt.Printf("%d:%x", i, ch)
    }
    fmt.Println()
    fmt.Println("-------------")
    //遍历所有字符
    count := 0
    for i, ch := range []rune(s){
        fmt.Printf("%d:%c", i, ch)
        count++
    }
    fmt.Println()
    fmt.Println("字符串长度", count)
    fmt.Println("字符串长度", utf8.RuneCountInString(s))
}
```

运行结果如下：

字节长度 14
---------
0:我 3:爱 6:G7:o8:语 11:言
字符串长度 6
------------
0:e61:882:913:e74:885:b16:477:6f8:e89:af10:ad11:e812:a813:80
-------------
0:我 1:爱 2:G3:o4:语 5:言
字符串长度 6
字符串长度 6

通过上面的示例可以看出，如果字符串涉及中文，遍历字符推荐使用 rune。因为一个 byte 存不下一个汉语文字的 unicode 值。