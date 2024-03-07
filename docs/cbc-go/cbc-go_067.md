# Go 语言为任意类型添加方法

Go 语言可以对任何类型添加方法。给一种类型添加方法就像给结构体添加方法一样，因为结构体也是一种类型。

## 为基本类型添加方法

在 Go 语言中，使用 type 关键字可以定义出新的自定义类型。之后就可以为自定义类型添加各种方法。我们习惯于使用面向过程的方式判断一个值是否为 0，例如：

```
if  v == 0 {
    // v 等于 0
}
```

如果将 v 当做整型对象，那么判断 v 值就可以增加一个 IsZero() 方法，通过这个方法就可以判断 v 值是否为 0，例如：

```
if  v.IsZero() {
    // v 等于 0
}
```

为基本类型添加方法的详细实现流程如下：

```
package main

import (
    "fmt"
)

// 将 int 定义为 MyInt 类型
type MyInt int

// 为 MyInt 添加 IsZero()方法
func (m MyInt) IsZero() bool {
    return m == 0
}

// 为 MyInt 添加 Add()方法
func (m MyInt) Add(other int) int {
    return other + int(m)
}

func main() {

    var b MyInt

    fmt.Println(b.IsZero())

    b = 1

    fmt.Println(b.Add(2))
}
```

代码输出如下：
true
3

代码说明如下：

*   第 8 行，使用 type MyInt int 将 int 定义为自定义的 MyInt 类型。
*   第 11 行，为 MyInt 类型添加 IsZero() 方法。该方法使用了 (m MyInt) 的非指针接收器。数值类型没有必要使用指针接收器。
*   第 16 行，为 MyInt 类型添加 Add() 方法。
*   第 17 行，由于 m 的类型是 MyInt 类型，但其本身是 int 类型，因此可以将 m 从 MyInt 类型转换为 int 类型再进行计算。
*   第 24 行，调用 b 的 IsZero() 方法。由于使用非指针接收器，b 的值会被复制进入 IsZero() 方法进行判断。
*   第 28 行，调用 b 的 Add() 方法。同样也是非指针接收器，结果直接通过 Add() 方法返回。

## http 包中的类型方法

Go 语言提供的 http 包里也大量使用了类型方法。Go 语言使用 http 包进行 HTTP 的请求，使用 http 包的 NewRequest() 方法可以创建一个 HTTP 请求，填充请求中的 http 头（req.Header），再调用 http.Client 的 Do 包方法，将传入的 HTTP 请求发送出去。

下面代码演示创建一个 HTTP 请求，并且设定 HTTP 头。

```
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "os"
    "strings"
)

func main() {
    client := &http.Client{}

    // 创建一个 http 请求
    req, err := http.NewRequest("POST", "http://www.163.com/", strings.NewReader("key=value"))

    // 发现错误就打印并退出
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
        return
    }

    // 为标头添加信息
    req.Header.Add("User-Agent", "myClient")

    // 开始请求
    resp, err := client.Do(req)

    // 处理请求的错误
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
        return
    }

    data, err := ioutil.ReadAll(resp.Body)
    fmt.Println(string(data))

    defer resp.Body.Close()

}
```

代码执行结果如下：

<html>
<head><title>405 Not Allowed</title></head>
<body bgcolor="white">
<center><h1>405 Not Allowed</h1></center>
<hr><center>nginx</center>
</body>
</html>

代码说明如下：

*   第 11 行，实例化 HTTP 的客户端，请求需要通过这个客户端实例发送。
*   第 14 行，使用 POST 方式向网易的服务器创建一个 HTTP 请求，第三个参数为 HTTP 的 Body 部分。Body 部分的内容来自字符串，但参数只能接受 io.Reader 类型，因此使用 strings.NewReader() 创建一个字符串的读取器，返回的 io.Reader 接口作为 http 的 Body 部分供 NewRequest() 函数读取。创建请求只是构造一个请求对象，不会连接网络。
*   第 24 行，为创建好的 HTTP 请求的头部添加 User-Agent，作用是表明用户的代理特性。
*   第 27 行，使用客户端处理请求，此时 client 将 HTTP 请求发送到网易服务器。服务器响应请求后，将信息返回并保存到 resp 变量中。
*   第 37 行，读取响应的 Body 部分并打印。

由于我们构造的请求不是网易服务器所支持的类型，所以服务器返回操作不被运行的 405 号错误。

在本例子第 24 行中使用的 req.Header 的类型为 http.Header，就是典型的自定义类型，并且拥有自己的方法。http.Header 的部分定义如下：

```
type Header map[string][]string

func (h Header) Add(key, value string) {
    textproto.MIMEHeader(h).Add(key, value)
}

func (h Header) Set(key, value string) {
    textproto.MIMEHeader(h).Set(key, value)
}

func (h Header) Get(key string) string {
    return textproto.MIMEHeader(h).Get(key)
}
```

代码说明如下：

*   第 1 行，Header 实际是一个以字符串为键、字符串切片为值的映射。
*   第 3 行，Add() 为 Header 的方法，map 是一个引用类型，因此即便使用 (h Header) 的非指针接收器，也可以修改 map 的值。

为类型添加方法的过程是一个语言层特性，使用类型方法的代码经过编译器编译后的代码运行效率与传统的面向过程或面向对象的代码没有任何区别。因此，为了代码便于理解，可以在编码时使用 Go 语言的类型方法特性。

## time 包中的类型方法

Go 语言提供的 time 包主要用于时间的获取和计算等。在这个包中，也使用了类型方法，例如：

```
package main

import (
    "fmt"
    "time"
)

func main() {
    fmt.Println(time.Second.String())
}
```

第 9 行的 time.Second 是一个常量，下面代码的加粗部分就是 time.Second 的定义：

```
const (
    Nanosecond  Duration = 1
    Microsecond  = 1000 * Nanosecond
    Millisecond  = 1000 * Microsecond
    Second       = 1000 * Millisecond
    Minute       = 60 * Second
    Hour         = 60 * Minute
)
```

Second 的类型为 Duration，而 Duration 实际是一个 int64 的类型，定义如下：

```
type Duration int64
```

它拥有一个 String 的方法，部分定义如下：

```
func (d Duration) String() string {

    // 一系列生成 buf 的代码
    …

    return string(buf[w:])
}
```

Duration.String 可以将 Duration 的值转为字符串。