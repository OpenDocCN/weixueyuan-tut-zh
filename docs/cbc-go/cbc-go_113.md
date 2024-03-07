# Go 语言 reflect.ValueOf()和 reflect.Value（通过反射获取值信息）

反射不仅可以获取值的类型信息，还可以动态地获取或者设置变量的值。Go 语言中使用 reflect.Value 获取和设置变量的值。

## 使用反射值对象包装任意值

Go 语言中，使用 reflect.ValueOf() 函数获得值的反射值对象（reflect.Value）。书写格式如下：

value := reflect.ValueOf(rawValue)

reflect.ValueOf 返回 reflect.Value 类型，包含有 rawValue 的值信息。reflect.Value 与原值间可以通过值包装和值获取互相转化。reflect.Value 是一些反射操作的重要类型，如反射调用函数。

## 从反射值对象获取被包装的值

Go 语言中可以通过 reflect.Value 重新获得原始值。

#### 1) 从反射值对象（reflect.Value）中获取值的方法

可以通过下面几种方法从反射值对象 reflect.Value 中获取原值，如下表所示。

反射值获取原始值的方法

| 方法名 | 说  明 |
| Interface() interface {} | 将值以 interface{} 类型返回，可以通过类型断言转换为指定类型 |
| Int() int64 | 将值以 int 类型返回，所有有符号整型均可以此方式返回 |
| Uint() uint64 | 将值以 uint 类型返回，所有无符号整型均可以此方式返回 |
| Float() float64 | 将值以双精度（float64）类型返回，所有浮点数（float32、float64）均可以此方式返回 |
| Bool() bool | 将值以 bool 类型返回 |
| Bytes() []bytes | 将值以字节数组 []bytes 类型返回 |
| String() string | 将值以字符串类型返回 |

#### 2) 从反射值对象（reflect.Value）中获取值的例子

下面代码中，将整型变量中的值使用 reflect.Value 获取反射值对象（reflect.Value）。再通过 reflect.Value 的 Interface() 方法获得 interface{} 类型的原值，通过 int 类型对应的 reflect.Value 的 Int() 方法获得整型值。

```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    // 声明整型变量 a 并赋初值
    var a int = 1024

    // 获取变量 a 的反射值对象
    valueOfA := reflect.ValueOf(a)

    // 获取 interface{}类型的值, 通过类型断言转换
    var getA int = valueOfA.Interface().(int)

    // 获取 64 位的值, 强制类型转换为 int 类型
    var getA2 int = int(valueOfA.Int())

    fmt.Println(getA, getA2)
}
```

代码输出如下：
1024 1024

代码说明如下：

*   第 11 行，声明一个变量，类型为 int，设置初值为 1024。
*   第 14 行，获取变量 a 的反射值对象，类型为 reflect.Value，这个过程和 reflect.TypeOf() 类似。
*   第 17 行，将 valueOfA 反射值对象以 interface{} 类型取出，通过类型断言转换为 int 类型并赋值给 getA。
*   第 20 行，将 valueOfA 反射值对象通过 Int 方法，以 int64 类型取出，通过强制类型转换，转换为原本的 int 类型。