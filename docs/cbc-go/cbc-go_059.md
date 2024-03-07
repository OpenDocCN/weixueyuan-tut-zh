# Go 语言宕机（panic）——程序终止运行

宕机不是一件很好的事情，可能造成体验停止、服务中断，就像没有人希望在取钱时遇到 ATM 机蓝屏一样。但是，如果在损失发生时，程序没有因为宕机而停止，那么用户将会付出更大的代价，这种代价可以是金钱、时间甚至生命。因此，宕机有时是一种合理的止损方法。

## 手动触发宕机

Go 语言可以在程序中手动触发宕机，让程序崩溃，这样开发者可以及时地发现错误，同时减少可能的损失。

Go 语言程序在宕机时，会将堆栈和 goroutine 信息输出到控制台，所以宕机也可以方便地知晓发生错误的位置。如果在编译时加入的调试信息甚至连崩溃现场的变量值、运行状态都可以获取，那么如何触发宕机呢？例如下面的代码：

```
package main

func main() {
    panic("crash")
}
```

代码运行崩溃并输出如下：

panic: crash

goroutine 1 [running]:
main.main()
    F:/src/tester/main.go:5 +0x6b

以上代码中只用了一个内建的函数 panic() 就可以造成崩溃，panic() 的声明如下：

```
func panic(v interface{})
```

panic() 的参数可以是任意类型，后文将提到的 recover 参数会接收从 panic() 中发出的内容。

## 在运行依赖的必备资源缺失时主动触发宕机

regexp 是 Go 语言的正则表达式包，正则表达式需要编译后才能使用，而且编译必须是成功的，表示正则表达式可用。

编译正则表达式函数有两种，具体如下：

#### 1) func Compile(expr string) (*Regexp, error)

编译正则表达式，发生错误时返回编译错误，Regexp 为 nil，该函数适用于在编译错误时获得编译错误进行处理，同时继续后续执行的环境。

#### 2) func MustCompile(str string) *Regexp

当编译正则表达式发生错误时，使用 panic 触发宕机，该函数适用于直接适用正则表达式而无须处理正则表达式错误的情况。

MustCompile 的代码如下：

```
func MustCompile(str string) *Regexp {
    regexp, error := Compile(str)
    if error != nil {
        panic(`regexp: Compile(` + quote(str) + `): ` + error.Error())
    }
    return regexp
}
```

代码说明如下：

*   第 1 行，编译正则表达式函数入口，输入包含正则表达式的字符串，返回正则表达式对象。
*   第 2 行，调用 Compile() 是编译正则表达式的入口函数，该函数返回编译好的正则表达式对象和错误。
*   第 3 和第 4 行判断如果有错，则使用 panic() 触发宕机。
*   第 6 行，没有错误时返回正则表达式对象。

手动宕机进行报错的方式不是一种偷懒的方式，反而能迅速报错，终止程序继续运行，防止更大的错误产生。不过，如果任何错误都使用宕机处理，也不是一种良好的设计。因此应根据需要来决定是否使用宕机进行报错。

## 在宕机时触发延迟执行语句

当 panic() 触发的宕机发生时，panic() 后面的代码将不会被运行，但是在 panic() 函数前面已经运行过的 defer 语句依然会在宕机发生时发生作用，参考下面代码：

```
package main

import "fmt"

func main() {
    defer fmt.Println("宕机后要做的事情 1")
    defer fmt.Println("宕机后要做的事情 2")
    panic("宕机")
}
```

代码输出如下：

宕机后要做的事情 2
宕机后要做的事情 1
panic: 宕机

goroutine 1 [running]:
main.main()
    F:/src/tester/main.go:8 +0x1a4

对代码的说明：

*   第 6 行和第 7 行使用 defer 语句延迟了 2 个语句。
*   第 8 行发生宕机。

宕机前，defer 语句会优先被执行，由于第 7 行的 defer 后执行，因此会在宕机前，这个 defer 会优先处理，随后才是第 6 行的 defer 对应的语句。这个特性可以用来在宕机发生前进行宕机信息处理。