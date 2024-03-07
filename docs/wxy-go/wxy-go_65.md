# Go 语言正则表达式（regexp 包）常用函数介绍

> 原文：[`www.weixueyuan.net/a/596.html`](http://www.weixueyuan.net/a/596.html)

在 Go 语言中想要使用正则表达式就需要引入 regexp 包，regexp 包中提供了一系列正则表达式函数，本节就来介绍一下。

1) 检查正则表达式与字节数组是否匹配。更复杂的查询建议使用 Compile() 和完整的 Regexp 接口。具体声明方式如下所示。

func Match(pattern string, b []byte) (matched bool, err error)

使用方式如下所示。

flag, _ := regexp.Match("^\\d{6, 15}$", []byte("123456789"))

返回结果：true。

flag, _ := regexp.Match("^\\d{6, 7}$", []byte("123456789"))

返回结果：false。

2) 检查正则表达式与字符串是否匹配。具体声明方式如下所示。

func MatchString(pattern string, s string) (matched bool, err error)

使用方式如下所示。

flag, _ := regexp.MatchString("^\\d{6, 15}$", "123456789")

返回结果：true

flag, _ := regexp.MatchString("^\\d{6, 7}$", "123456789")

返回结果：false

3) 将正则表达式字符串编译成正则表达式对象（Regexp）。具体声明方式如下所示。

func Compile(expr string) (*Regexp, error)

具体使用方式如下所示。

MyRegexp, _ := regexp.Compile("^\\d{6}\\D{2}$")

4) MustCompile() 用法同 Compile()，但是不返回 error。如果表达式不能被解析就会 panic。它简化了正则表达式字符串编译成正则表达式对象的过程。具体声明方式如下所示。

func MustCompile(str string) *Regexp

具体使用方式如下所示。

MyRegexp := regexp.MustCompile("^[\u4e00-\u9fa5]+$")

5) 判断 Regexp 正则对象是否与给定的字节数组匹配。具体声明方式如下所示。

func (re *Regexp) Match(b []byte) bool

具体使用方式如下所示。

MyRegexp := regexp.MustCompile("^[\u4e00-\u9fa5]+$")
flag = MyRegexp.Match([]byte("一丁 "))

返回结果：true。

6) 判断 Regexp 正则对象是否与给定的字符串匹配。具体声明格式如下所示。

func (re *Regexp) MatchString(s string) bool

具体使用方式如下所示。

MyRegexp, _ := regexp.Compile("^\\d{6}\\D{2}$")
flag = RegExp.MatchString("123456ab")

返回结果：true。

7) ReplaceAll() 将 src 中符合正则表达式的部分全部替换成指定内容。

func (re *Regexp) ReplaceAll(src, repl []byte) []byte

具体使用方式如下所示。

text := "将字符串 123 中符合正则表达式的内容 3 4 5 全部替换成 56 78 指定的内容"
MyRegexp := regexp.MustCompile("[\\a\\s]+")
result := string(MyRegexp.ReplaceAll([]byte(text), []byte("")))

本案例中将返回结果转为 string，结果为：“将字符串中符合正则表达式的内容全部替换成指定的内容”。

8) 将字符串按照正则表达式分割成子字符串组成的切片。如果切片长度超过指定参数 n，则不再分割。具体声明方式如下所示。

func (re *Regexp) Split(s string, n int) []string

具体使用方式如下所示。

text = "第一部分#第二部分##第三部分###第四部分#第五部分##第六部分"
MyRegexp := regexp.MustCompile("#+")
arr := MyRegexp.Split(text, 5)

返回结果为字符串组成的切片，本示例中只切割成 5 个元素，因此结果如下所示。

[第一部分 第二部分 第三部分 第四部分 第五部分##第六部分]

接下来通过一个示例来演示部分正则表达式函数的使用方式，代码如下所示。

```

package main
import(
    "fmt"
    "regexp"
)
func main(){
    testRegexp()
}
func testRegexp(){
    //1\. Match(pattern string, b[]byte)(matched bool, err error)检查正则表达式是否与字节切片匹配
    flag, _ := regexp.Match("^\\d{6, 7}$", []byte("123456789"))
    fmt.Println(flag)
    //2\. MatchString(pattern string, s string) (matched bool, err error)
    flag, _ = regexp.MatchString("^\\a{6, 7}$", "0123456789")
    fmt.Println(flag)
    //3\. Compile(expr string) (*Regexp, error)
    RegExp, _ := regexp.Compile("^\\d{6}\\D{2}$")
    //4\. MustCompile(str string) *Regexp
    RegExp2 := regexp.MustCompile("^[\u4e00-\u9fa5]+$")
    //5\. Match(b[]byte) bool
    flag = RegExp2.Match([]byte("一丁  "))
    fmt.Println("xxx:", flag)
    //6\. MatchString(s string)bool
    flag = RegExp.MatchString("123456ab")
    fmt.Println(flag)
    //7\. ReplaceAll(src, repl []byte) []byte
    text := "将字符串 123 按照正则表达式 3 4 5 分割成子字符串 56 78 组成的切片"
    RegExp3 := regexp.MustCompile("[\\d\\s]+")
    result := string(RegExp3.ReplaceAll([]byte(text), []byte("-")))
    fmt.Println("替换后的字符串为:", result)
    text = "第一部分#第二部分##第三部分###第四部分#第五部分##第六部分"
    MyRegexp := regexp.MustCompile("#+")
    arr := MyRegexp.Split(text, 5)
    fmt.Println(arr)
}
```

运行结果如下：

false
false
xxx: false
true
替换后的字符串为: 将字符串-按照正则表达式-分割成子字符串-组成的切片
[第一部分 第二部分 第三部分 第四部分 第五部分##第六部分]

上面示例中，第 11 行代码中的正则表达式 `^\\d{6,7}$`，表示以数字开头，以数字结尾，且长度在 6 和 7 之间，而字节切片 123456789 的长度不在这个范围内，所以输出 false；第 21 行正则对象 RegExp2 的规则是 `^[\u4e00-\u9fa5]+$`，代表所有字符都是中文，所以输出 true；第 24 行正则对象 RegExp 的规则是 `^\\d{6}\\D{2}$`，表示以 6 个数字开头，并以 2 个非数字结尾。显然 123456ab 符合这个规则，输出 true；第 29 行表示将字节数组中的所有连续的数字替换成“-”；第 33 行表示将字符串中的“#”一个或多个作为分割符把字符串分割成 5 份，5 份以后再出现“#”也不会分割。