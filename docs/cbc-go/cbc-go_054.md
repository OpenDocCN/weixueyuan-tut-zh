# Go 语言函数类型实现接口——把函数作为接口来调用

函数和其他类型一样都属于“一等公民”，其他类型能够实现接口，函数也可以，本节将分别对比结构体与函数实现接口的过程。

首先给出本节完整的代码：

```
package main

import (
    "fmt"
)

// 调用器接口
type Invoker interface {
    // 需要实现一个 Call 方法
    Call(interface{})
}

// 结构体类型
type Struct struct {
}

// 实现 Invoker 的 Call
func (s *Struct) Call(p interface{}) {
    fmt.Println("from struct", p)
}

// 函数定义为类型
type FuncCaller func(interface{})

// 实现 Invoker 的 Call
func (f FuncCaller) Call(p interface{}) {

    // 调用 f 函数本体
    f(p)
}

func main() {

    // 声明接口变量
    var invoker Invoker

    // 实例化结构体
    s := new(Struct)

    // 将实例化的结构体赋值到接口
    invoker = s

    // 使用接口调用实例化结构体的方法 Struct.Call
    invoker.Call("hello")

    // 将匿名函数转为 FuncCaller 类型，再赋值给接口
    invoker = FuncCaller(func(v interface{}) {
        fmt.Println("from function", v)
    })

    // 使用接口调用 FuncCaller.Call，内部会调用函数本体
    invoker.Call("hello")
}
```

有如下一个接口：

```
// 调用器接口
type Invoker interface {
    // 需要实现一个 Call()方法
    Call(interface{})
}
```

这个接口需要实现 Call() 方法，调用时会传入一个 interface{} 类型的变量，这种类型的变量表示任意类型的值。

接下来，使用结构体进行接口实现。

## 结构体实现接口

结构体实现 Invoker 接口的代码如下：

```
// 结构体类型
type Struct struct {
}

// 实现 Invoker 的 Call
func (s *Struct) Call(p interface{}) {
    fmt.Println("from struct", p)
}
```

代码说明如下：

*   第 2 行，定义结构体，该例子中的结构体无须任何成员，主要展示实现 Invoker 的方法。
*   第 6 行，Call() 为结构体的方法，该方法的功能是打印 from struct 和传入的 interface{} 类型的值。

将定义的 Struct 类型实例化，并传入接口中进行调用，代码如下：

```
// 声明接口变量
var invoker Invoker

// 实例化结构体
s := new(Struct)

// 将实例化的结构体赋值到接口
invoker = s

// 使用接口调用实例化结构体的方法 Struct.Call
invoker.Call("hello")
```

代码说明如下：

*   第 2 行，声明 Invoker 类型的变量。
*   第 5 行，使用 new 将结构体实例化，此行也可以写为 s:=&Struct。
*   第 8 行，s 类型为 *Struct，已经实现了 Invoker 接口类型，因此赋值给 invoker 时是成功的。
*   第 11 行，通过接口的 Call() 方法，传入 hello，此时将调用 Struct 结构体的 Call() 方法。

接下来，对比下函数实现结构体的差异。

代码输出如下：
from struct hello

## 函数体实现接口

函数的声明不能直接实现接口，需要将函数定义为类型后，使用类型实现结构体。当类型方法被调用时，还需要调用函数本体。

```
// 函数定义为类型
type FuncCaller func(interface{})

// 实现 Invoker 的 Call
func (f FuncCaller) Call(p interface{}) {

    // 调用 f()函数本体
    f(p)
}
```

代码说明如下：

*   第 2 行，将 func(interface{}) 定义为 FuncCaller 类型。
*   第 5 行，FuncCaller 的 Call() 方法将实现 Invoker 的 Call() 方法。
*   第 8 行，FuncCaller 的 Call() 方法被调用与 func(interface{}) 无关，还需要手动调用函数本体。

上面代码只是定义了函数类型，需要函数本身进行逻辑处理。FuncCaller 无须被实例化，只需要将函数转换为 FuncCaller 类型即可，函数来源可以是命名函数、匿名函数或闭包，参见下面代码：

```
// 声明接口变量
var invoker Invoker

// 将匿名函数转为 FuncCaller 类型, 再赋值给接口
invoker = FuncCaller(func(v interface{}) {
    fmt.Println("from function", v)
})

// 使用接口调用 FuncCaller.Call, 内部会调用函数本体
invoker.Call("hello")
```

代码说明如下：

*   第 2 行，声明接口变量。
*   第 5 行，将 func(v interface{}){} 匿名函数转换为 FuncCaller 类型（函数签名才能转换），此时 FuncCaller 类型实现了 Invoker 的 Call() 方法，赋值给 invoker 接口是成功的。
*   第 10 行，使用接口方法调用。

代码输出如下：
from function hello

## HTTP 包中的例子

HTTP 包中包含有 Handler 接口定义，代码如下：

```
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

Handler 用于定义每个 HTTP 的请求和响应的处理过程。

同时，也可以使用处理函数实现接口，定义如下：

```
type HandlerFunc func(ResponseWriter, *Request)

func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
```

要使用闭包实现默认的 HTTP 请求处理，可以使用 http.HandleFunc() 函数，函数定义如下：

```
func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    DefaultServeMux.HandleFunc(pattern, handler)
}
```

而 DefaultServeMux 是 ServeMux 结构，拥有 HandleFunc() 方法，定义如下：

```
func (mux *ServeMux) HandleFunc(pattern string, handler func
(ResponseWriter, *Request)) {
    mux.Handle(pattern, HandlerFunc(handler))
}
```

上面代码将外部传入的函数 handler() 转为 HandlerFunc 类型，HandlerFunc 类型实现了 Handler 的 ServeHTTP 方法，底层可以同时使用各种类型来实现 Handler 接口进行处理。