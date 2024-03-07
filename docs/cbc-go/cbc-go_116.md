# Go 语言通过反射修改变量的值

使用 reflect.Value 对包装的值进行修改时，需要遵循一些规则。如果没有按照规则进行代码设计和编写，轻则无法修改对象值，重则程序在运行时会发生宕机。

## 判定及获取元素的相关方法

使用 reflect.Value 取元素、取地址及修改值的属性方法请参考下表。

反射值对象的判定及获取元素的方法

| 方法名 | 备  注 |
| Elem() Value | 取值指向的元素值，类似于语言层`*`操作。当值类型不是指针或接口时发生宕 机，空指针时返回 nil 的 Value |
| Addr() Value | 对可寻址的值返回其地址，类似于语言层`&`操作。当值不可寻址时发生宕机 |
| CanAddr() bool | 表示值是否可寻址 |
| CanSet() bool | 返回值能否被修改。要求值可寻址且是导出的字段 |

## 值修改相关方法

使用 reflect.Value 修改值的相关方法如下表所示。

反射值对象修改值的方法

| Set(x Value) | 将值设置为传入的反射值对象的值 |
| Setlnt(x int64) | 使用 int64 设置值。当值的类型不是 int、int8、int16、 int32、int64 时会发生宕机 |
| SetUint(x uint64) | 使用 uint64 设置值。当值的类型不是 uint、uint8、uint16、uint32、uint64 时会发生宕机 |
| SetFloat(x float64) | 使用 float64 设置值。当值的类型不是 float32、float64 时会发生宕机 |
| SetBool(x bool) | 使用 bool 设置值。当值的类型不是 bod 时会发生宕机 |
| SetBytes(x []byte) | 设置字节数组 []bytes 值。当值的类型不是 []byte 时会发生宕机 |
| SetString(x string) | 设置字符串值。当值的类型不是 string 时会发生宕机 |

以上方法，在 reflect.Value 的 CanSet 返回 false 仍然修改值时会发生宕机。

在已知值的类型时，应尽量使用值对应类型的反射设置值。

## 值可修改条件之一：可被寻址

通过反射修改变量值的前提条件之一：这个值必须可以被寻址。简单地说就是这个变量必须能被修改。示例代码如下：

```
package main

import (
    "reflect"
)

func main() {

    // 声明整型变量 a 并赋初值
    var a int = 1024

    // 获取变量 a 的反射值对象
    valueOfA := reflect.ValueOf(a)

    // 尝试将 a 修改为 1(此处会发生崩溃)
    valueOfA.SetInt(1)
}
```

程序运行崩溃，打印错误：

panic: reflect: reflect.Value.SetInt using unaddressable value

报错意思是：SetInt 正在使用一个不能被寻址的值。从 reflect.ValueOf 传入的是 a 的值，而不是 a 的地址，这个 reflect.Value 当然是不能被寻址的。将代码修改一下，重新运行：

```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    // 声明整型变量 a 并赋初值
    var a int = 1024

    // 获取变量 a 的反射值对象(a 的地址)
    valueOfA := reflect.ValueOf(&a)

    // 取出 a 地址的元素(a 的值)
    valueOfA = valueOfA.Elem()

    // 修改 a 的值为 1
    valueOfA.SetInt(1)

    // 打印 a 的值
    fmt.Println(valueOfA.Int())
}
```

代码输出如下：
1

下面是对代码的分析：

*   第 14 行中，将变量 a 取值后传给 reflect.ValueOf()。此时 reflect.ValueOf() 返回的 valueOfA 持有变量 a 的地址。
*   第 17 行中，使用 reflect.Value 类型的 Elem() 方法获取 a 地址的元素，也就是 a 的值。reflect.Value 的 Elem() 方法返回的值类型也是 reflect.Value。
*   第 20 行，此时 valueOfA 表示的是 a 的值且可以寻址。使用 SetInt() 方法设置值时不再发生崩溃。
*   第 23 行，正确打印修改的值。

#### 提示

当 reflect.Value 不可寻址时，使用 Addr() 方法也是无法取到值的地址的，同时会发生宕机。虽然说 reflect.Value 的 Addr() 方法类似于语言层的`&`操作；Elem() 方法类似于语言层的`*`操作，但并不代表这些方法与语言层操作等效。

## 值可修改条件之一：被导出

结构体成员中，如果字段没有被导出，即便不使用反射也可以被访问，但不能通过反射修改，代码如下：

```
package main

import (
    "reflect"
)

func main() {

    type dog struct {
            legCount int
    }
    // 获取 dog 实例的反射值对象
    valueOfDog := reflect.ValueOf(dog{})

    // 获取 legCount 字段的值
    vLegCount := valueOfDog.FieldByName("legCount")

    // 尝试设置 legCount 的值(这里会发生崩溃)
    vLegCount.SetInt(4)
}
```

程序发生崩溃，报错：

panic: reflect: reflect.Value.SetInt using value obtained using unexported field

报错的意思是：SetInt() 使用的值来自于一个未导出的字段。

为了能修改这个值，需要将该字段导出。将 dog 中的 legCount 的成员首字母大写，导出 LegCount 让反射可以访问，修改后的代码如下：

```
type dog struct {
    LegCount int
}
```

然后根据字段名获取字段的值时，将字符串的字段首字母大写，修改后的代码如下：

```
vLegCount := valueOfDog.FieldByName("LegCount")
```

再次运行程序，发现仍然报错：

panic: reflect: reflect.Value.SetInt using unaddressable value

这个错误表示第 13 行构造的 valueOfDog 这个结构体实例不能被寻址，因此其字段也不能被修改。修改代码，取结构体的指针，再通过 reflect.Value 的 Elem() 方法取到值的反射值对象。修改后的完整代码如下：

```
package main

import (
    "reflect"
    "fmt"
)

func main() {

    type dog struct {
            LegCount int
    }
    // 获取 dog 实例地址的反射值对象
    valueOfDog := reflect.ValueOf(&dog{})

    // 取出 dog 实例地址的元素
    valueOfDog = valueOfDog.Elem()

    // 获取 legCount 字段的值
    vLegCount := valueOfDog.FieldByName("LegCount")

    // 尝试设置 legCount 的值(这里会发生崩溃)
    vLegCount.SetInt(4)

    fmt.Println(vLegCount.Int())
}
```

代码输出如下：
4

代码说明如下：

*   第 11 行，将 LegCount 首字母大写导出该字段。
*   第 14 行，获取 dog 实例指针的反射值对象。
*   第 17 行，取 dog 实例的指针元素，也就是 dog 的实例。
*   第 20 行，取 dog 结构体中 LegCount 字段的成员值。
*   第 23 行，修改该成员值。
*   第 25 行，打印该成员值。

值的修改从表面意义上叫可寻址，换一种说法就是值必须“可被设置”。那么，想修改变量值，一般的步骤是：

1.  取这个变量的地址或者这个变量所在的结构体已经是指针类型。
2.  使用 reflect.ValueOf 进行值包装。
3.  通过 Value.Elem() 获得指针值指向的元素值对象（Value），因为值对象（Value）内部对象为指针时，使用 set 设置时会报出宕机错误。
4.  使用 Value.Set 设置值。