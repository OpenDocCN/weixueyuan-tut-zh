# Go 语言接口的 nil 判断

nil 在 Go 语言中只能被赋值给指针和接口。接口在底层的实现有两个部分：type 和 data。在源码中，显式地将 nil 赋值给接口时，接口的 type 和 data 都将为 nil。此时，接口与 nil 值判断是相等的。但如果将一个带有类型的 nil 赋值给接口时，只有 data 为 nil，而 type 为 nil，此时，接口与 nil 判断将不相等。

## 接口与 nil 不相等

下面代码使用 MyImplement() 实现 fmt 包中的 Stringer 接口，这个接口的定义如下：

```
type Stringer interface {
    String() string
}
```

在 GetStringer() 函数中将返回这个接口。通过 *MyImplement 指针变量置为 nil 提供 GetStringer 的返回值。在 main() 中，判断 GetStringer 与 nil 是否相等，代码如下：

```
package main

import "fmt"

// 定义一个结构体
type MyImplement struct{}

// 实现 fmt.Stringer 的 String 方法
func (m *MyImplement) String() string {

    return "hi"
}

// 在函数中返回 fmt.Stringer 接口
func GetStringer() fmt.Stringer {

    // 赋 nil
    var s *MyImplement = nil

    // 返回变量
    return s
}

func main() {

    // 判断返回值是否为 nil
    if GetStringer() == nil {
        fmt.Println("GetStringer() == nil")
    } else {
        fmt.Println("GetStringer() != nil")
    }

}
```

代码说明如下：

*   第 9 行，实现 fmt.Stringer 的 String() 方法。
*   第 21 行，s 变量此时被 fmt.Stringer 接口包装后，实际类型为 *MyImplement，值为 nil 的接口。
*   第 27 行，使用 GetStringer() 的返回值与 nil 判断时，虽然接口里的 value 为 nil，但 type 带有 *MyImplement 信息，使用 == 判断相等时，依然不为 nil。

## 发现 nil 类型值返回时直接返回 nil

为了避免这类误判的问题，可以在函数返回时，发现带有 nil 的指针时直接返回 nil，代码如下：

```
func GetStringer() fmt.Stringer {

    var s *MyImplement = nil

    if s == nil {
        return nil
    }

    return s
}
```