# Go 语言函数变量（把函数作为值保存到变量中）

> 原文：[`www.weixueyuan.net/a/524.html`](http://www.weixueyuan.net/a/524.html)

在 Go 语言中，函数也是一种类型，可以和其他类型（如 int32、float 等等）一样被保存在变量中。函数变量的使用方式如下所示。

```

package main
import (
    "fmt"
    "strings"
)
func main() {
    result := StringToLower("AbcdefGHijklMNOPqrstUVWxyz", processCase)
    fmt.Println(result)
    result = StringToLower2("AbcdefGHijklMNOPqrstUVWxyz", processCase)
    fmt.Println(result)
}
// 处理字符串，奇数偶数依次显示为大小写
func processCase(str string) string {
    result := ""
    for i, value := range str {
        if i%2 == 0 {
            result += strings.ToUpper(string(value))
        } else {
            result += strings.ToLower(string(value))
        }
    }
    return result
}
func StringToLower(str string, f func(string) string) string {
    fmt.Printf("%T \n", f)
    return f(str)
}
type caseFunc func(string) string //声明了一个函数类型，通过 type 关键字，caseFunc 会形成一个新的类型
func StringToLower2(str string, f caseFunc) string {
    fmt.Printf("%T \n", f) // 打印变量 f 的类型
    return f(str)
}
```

运行结果如下：

func(string) string
AbCdEfGhIjKlMnOpQrStUvWxYz
main.caseFunc
AbCdEfGhIjKlMnOpQrStUvWxYz

在上面的示例代码中，第 24 行声明了函数 StringToLower()，第二个传入参数为一个函数类型变量 f；第 7 行调用了函数 StringToLower()，第二个参数传入了在第 13 行声明的函数 processCase()。这样在函数 StringToLower() 中可以通过函数变量 f 执行函数 processCase()；第 28 行将参数相同的函数类型声明为新的类型 caseFunc，那么 caseFunc 就代表着这一种函数变量使用在 StringToLower2() 中。

下面在通过一个示例来演示一下函数变量的使用。

```

package main
import "fmt"
type procesFunc func(int) bool // 声明了一个函数类型
func main() {
    slice := []int{1, 2, 3, 4, 5, 7}
    fmt.Println("slice = ", slice)
    odd := filter(slice, isOdd) // 函数当作值来传递
    fmt.Println("奇数元素：", odd)
    even := filter(slice, isEven) // 函数当作值来传递
    fmt.Println("偶数元素：", even)
}
//判断元素是否是偶数
func isEven(integer int) bool {
    if integer%2 == 0 {
        return true
    }
    return false
}
//判断元素是否是奇数
func isOdd(integer int) bool {
    if integer%2 == 0 {
        return false
    }
    return true
}
//根据函数来处理切片，根据元素奇数偶数分组，返回新的切片
func filter(slice []int, f procesFunc) []int {
    var result []int
    for _, value := range slice {
        if f(value) {
            result = append(result, value)
        }
    }
    return result
}
```

运行结果如下：

slice =  [1 2 3 4 5 7]
奇数元素： [1 3 5 7]
偶数元素： [2 4]

函数变量的使用步骤及意义如下。

*   定义一个函数类型；
*   实现定义的函数类型；
*   作为参数调用。

函数当作值和类型在写一些通用接口的时候非常有用，通过上面的例子可以看到 processFunc 这个类型是一个函数类型，然后两个 filter 函数的参数和返回值与 processFunc 类型是一样的。用户可以实现很多种逻辑，这样使得程序变得非常灵活。