# Go 语言处理运行时错误

Go 语言的错误处理思想及设计包含以下特征：

*   一个可能造成错误的函数，需要返回值中返回一个错误接口（error）。如果调用是成功的，错误接口将返回 nil，否则返回错误。
*   在函数调用后需要检查错误，如果发生错误，进行必要的错误处理。

Go 语言没有类似 Java 或 .NET 中的异常处理机制，虽然可以使用 defer、panic、recover 模拟，但官方并不主张这样做。Go 语言的设计者认为其他语言的异常机制已被过度使用，上层逻辑需要为函数发生的异常付出太多的资源。同时，如果函数使用者觉得错误处理很麻烦而忽略错误，那么程序将在不可预知的时刻崩溃。

Go 语言希望开发者将错误处理视为正常开发必须实现的环节，正确地处理每一个可能发生错误的函数。同时，Go 语言使用返回值返回错误的机制，也能大幅降低编译器、运行时处理错误的复杂度，让开发者真正地掌握错误的处理。

## net 包中的例子

net.Dial() 是 Go 语言系统包 net 即中的一个函数，一般用于创建一个 Socket 连接。

net.Dial 拥有两个返回值，即 Conn 和 error。这个函数是阻塞的，因此在 Socket 操作后，会返回 Conn 连接对象和 error；如果发生错误，error 会告知错误的类型，Conn 会返回空。

根据 Go 语言的错误处理机制，Conn 是其重要的返回值。因此，为这个函数增加一个错误返回，类似为 error。参见下面的代码：

```
func Dial(network, address string) (Conn, error) {
    var d Dialer
    return d.Dial(network, address)
}
```

在 io 包中的 Writer 接口也拥有错误返回，代码如下：

```
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

io 包中还有 Closer 接口，只有一个错误返回，代码如下：

```
type Closer interface {
    Close() error
}
```

## 错误接口的定义格式

error 是 Go 系统声明的接口类型，代码如下：

```
type error interface {
    Error() string
}
```

所有符合 Error()string 格式的方法，都能实现错误接口。

Error() 方法返回错误的具体描述，使用者可以通过这个字符串知道发生了什么错误。

## 自定义一个错误

返回错误前，需要定义会产生哪些可能的错误。在 Go 语言中，使用 errors 包进行错误的定义，格式如下：

```
var err = errors.New("this is an error")
```

错误字符串由于相对固定，一般在包作用域声明，应尽量减少在使用时直接使用 errors.New 返回。

#### 1) errors 包

Go 语言的 errors 中对 New 的定义非常简单，代码如下：

```
// 创建错误对象
func New(text string) error {
    return &errorString{text}
}

// 错误字符串
type errorString struct {
    s string
}

// 返回发生何种错误
func (e *errorString) Error() string {
    return e.s
}
```

代码说明如下：

*   第 2 行，将 errorString 结构体实例化，并赋值错误描述的成员。
*   第 7 行，声明 errorString 结构体，拥有一个成员，描述错误内容。
*   第 12 行，实现 error 接口的 Error() 方法，该方法返回成员中的错误描述。

#### 2) 在代码中使用错误定义

下面的代码会定义一个除法函数，当除数为 0 时，返回一个预定义的除数为 0 的错误。

```
package main

import (
    "errors"
    "fmt"
)

// 定义除数为 0 的错误
var errDivisionByZero = errors.New("division by zero")

func div(dividend, divisor int) (int, error) {

    // 判断除数为 0 的情况并返回
    if divisor == 0 {
        return 0, errDivisionByZero
    }

    // 正常计算，返回空错误
    return dividend / divisor, nil
}

func main() {

    fmt.Println(div(1, 0))
}
```

代码输出如下：
0 division by zero

代码说明：

*   第 9 行，预定义除数为 0 的错误。
*   第 11 行，声明除法函数，输入被除数和除数，返回商和错误。
*   第 14 行，在除法计算中，如果除数为 0，计算结果为无穷大。为了避免这种情况，对除数进行判断，并返回商为 0 和除数为 0 的错误对象。
*   第 19 行，进行正常的除法计算，没有发生错误时，错误对象返回 nil。

## 示例：在解析中使用自定义错误

使用 errors.New 定义的错误字符串的错误类型是无法提供丰富的错误信息的。那么，如果需要携带错误信息返回，就需要借助自定义结构体实现错误接口。

下面代码将实现一个解析错误（ParseError），这种错误包含两个内容：文件名和行号。解析错误的结构还实现了 error 接口的 Error() 方法，返回错误描述时，就需要将文件名和行号返回。

```
package main

import (
    "fmt"
)

// 声明一个解析错误
type ParseError struct {
    Filename string // 文件名
    Line     int    // 行号
}

// 实现 error 接口，返回错误描述
func (e *ParseError) Error() string {
    return fmt.Sprintf("%s:%d", e.Filename, e.Line)
}

// 创建一些解析错误
func newParseError(filename string, line int) error {
    return &ParseError{filename, line}
}

func main() {

    var e error
    // 创建一个错误实例，包含文件名和行号
    e = newParseError("main.go", 1)

    // 通过 error 接口查看错误描述
    fmt.Println(e.Error())

    // 根据错误接口具体的类型，获取详细错误信息
    switch detail := e.(type) {
    case *ParseError: // 这是一个解析错误
        fmt.Printf("Filename: %s Line: %d\n", detail.Filename, detail.Line)
    default: // 其他类型的错误
        fmt.Println("other error")
    }
}
```

代码输出如下：
main.go:1
Filename: main.go Line: 1

代码说明如下：

*   第 8 行，声明了一个解析错误的结构体，解析错误包含有 2 个成员：文件名和行号。
*   第 14 行，实现了错误接口，将成员的文件名和行号格式化为字符串返回。
*   第 19 行，根据给定的文件名和行号创建一个错误实例。
*   第 25 行，声明一个错误接口类型。
*   第 27 行，创建一个实例，这个错误接口内部是 *ParserError 类型，携带有文件名 main.go 和行号 1。
*   第 30 行，调用 Error() 方法，通过第 15 行返回错误的详细信息。
*   第 33 行，通过错误断言，取出发生错误的详细类型。
*   第 34 行，通过分析这个错误的类型，得知错误类型为 *ParserError，此时可以获取到详细的错误信息。
*   第 36 行，如果不是我们能够处理的错误类型，会打印出其他错误做出其他的处理。

错误对象都要实现 error 接口的 Error() 方法，这样，所有的错误都可以获得字符串的描述。如果想进一步知道错误的详细信息，可以通过类型断言，将错误对象转为具体的错误类型进行错误详细信息的获取。