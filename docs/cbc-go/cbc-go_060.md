# Go 语言宕机恢复（recover）——防止程序崩溃

无论是代码运行错误由 Runtime 层抛出的 panic 崩溃，还是主动触发的 panic 崩溃，都可以配合 defer 和 recover 实现错误捕捉和恢复，让代码在发生崩溃后允许继续运行。

#### 提示

在其他语言里，宕机往往以异常的形式存在。底层抛出异常，上层逻辑通过 try/catch 机制捕获异常，没有被捕获的严重异常会导致宕机，捕获的异常可以被忽略，让代码继续运行。

Go 没有异常系统，其使用 panic 触发宕机类似于其他语言的抛出异常，那么 recover 的宕机恢复机制就对应 try/catch 机制。

## 让程序在崩溃时继续执行

下面的代码实现了 ProtectRun() 函数，该函数传入一个匿名函数或闭包后的执行函数，当传入函数以任何形式发生 panic 崩溃后，可以将崩溃发生的错误打印出来，同时允许后面的代码继续运行，不会造成整个进程的崩溃。

保护运行函数：

```
package main

import (
    "fmt"
    "runtime"
)

// 崩溃时需要传递的上下文信息
type panicContext struct {
    function string // 所在函数
}

// 保护方式允许一个函数
func ProtectRun(entry func()) {

    // 延迟处理的函数
    defer func() {

        // 发生宕机时，获取 panic 传递的上下文并打印
        err := recover()

        switch err.(type) {
        case runtime.Error: // 运行时错误
            fmt.Println("runtime error:", err)
        default: // 非运行时错误
            fmt.Println("error:", err)
        }

    }()

    entry()

}

func main() {
    fmt.Println("运行前")

    // 允许一段手动触发的错误
    ProtectRun(func() {

        fmt.Println("手动宕机前")

        // 使用 panic 传递上下文
        panic(&panicContext{
            "手动触发 panic",
        })

        fmt.Println("手动宕机后")
    })

    // 故意造成空指针访问错误
    ProtectRun(func() {

        fmt.Println("赋值宕机前")

        var a *int
        *a = 1

        fmt.Println("赋值宕机后")
    })

    fmt.Println("运行后")
}
```

代码输出结果：

运行前
手动宕机前
error: &{手动触发 panic}
赋值宕机前
runtime error: runtime error: invalid memory address or nil pointer
dereference
运行后

对代码的说明：

*   第 9 行声明描述错误的结构体，成员保存错误的执行函数。
*   第 17 行使用 defer 将闭包延迟执行，当 panic 触发崩溃时，ProtectRun() 函数将结束运行，此时 defer 后的闭包将会发生调用。
*   第 20 行，recover() 获取到 panic 传入的参数。
*   第 22 行，使用 switch 对 err 变量进行类型断言。
*   第 23 行，如果错误是有 Runtime 层抛出的运行时错误，如空指针访问、除数为 0 等情况，打印运行时错误。
*   第 25 行，其他错误，打印传递过来的错误数据。
*   第 44 行，使用 panic 手动触发一个错误，并将一个结构体附带信息传递过去，此时，recover 就会获取到这个结构体信息，并打印出来。
*   第 57 行，模拟代码中空指针赋值造成的错误，此时会由 Runtime 层抛出错误，被 ProtectRun() 函数的 recover() 函数捕获到。

## panic 和 recover 的关系

panic 和 defer 的组合有如下特性：

*   有 panic 没 recover，程序宕机。
*   有 panic 也有 recover 捕获，程序不会宕机。执行完对应的 defer 后，从宕机点退出当前函数后继续执行。

#### 提示

虽然 panic/recover 能模拟其他语言的异常机制，但并不建议代表编写普通函数也经常性使用这种特性。

在 panic 触发的 defer 函数内，可以继续调用 panic，进一步将错误外抛直到程序整体崩溃。

如果想在捕获错误时设置当前函数的返回值，可以对返回值使用命名返回值方式直接进行设置。