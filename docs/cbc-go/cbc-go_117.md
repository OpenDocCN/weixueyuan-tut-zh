# Go 语言通过类型信息创建实例

当已知 reflect.Type 时，可以动态地创建这个类型的实例，实例的类型为指针。例如 reflect.Type 的类型为 int 时，创建 int 的指针，即`*int`，代码如下：

```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    var a int

    // 取变量 a 的反射类型对象
    typeOfA := reflect.TypeOf(a)

    // 根据反射类型对象创建类型实例
    aIns := reflect.New(typeOfA)

    // 输出 Value 的类型和种类
    fmt.Println(aIns.Type(), aIns.Kind())
}
```

代码输出如下：
*int ptr

代码说明如下：

*   第 13 行，获取变量 a 的反射类型对象。
*   第 16 行，使用 reflect.New() 函数传入变量 a 的反射类型对象，创建这个类型的实例值，值以 reflect.Value 类型返回。这步操作等效于：new(int)，因此返回的是 *int 类型的实例。
*   第 19 行，打印 aIns 的类型为 *int，种类为指针。