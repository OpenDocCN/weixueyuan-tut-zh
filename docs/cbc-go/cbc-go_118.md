# Go 语言通过反射调用函数

如果反射值对象（reflect.Value）中值的类型为函数时，可以通过 reflect.Value 调用该函数。使用反射调用函数时，需要将参数使用反射值对象的切片 []reflect.Value 构造后传入 Call() 方法中，调用完成时，函数的返回值通过 []reflect.Value 返回。

下面的代码声明一个加法函数，传入两个整型值，返回两个整型值的和。将函数保存到反射值对象（reflect.Value）中，然后将两个整型值构造为反射值对象的切片（[]reflect.Value），使用 Call() 方法进行调用。

反射调用函数：

```
package main

import (
    "fmt"
    "reflect"
)

// 普通函数
func add(a, b int) int {

    return a + b
}

func main() {

    // 将函数包装为反射值对象
    funcValue := reflect.ValueOf(add)

    // 构造函数参数, 传入两个整型值
    paramList := []reflect.Value{reflect.ValueOf(10), reflect.ValueOf(20)}

    // 反射调用函数
    retList := funcValue.Call(paramList)

    // 获取第一个返回值, 取整数值
    fmt.Println(retList[0].Int())
}
```

代码说明如下：

*   第 9～12 行，定义一个普通的加法函数。
*   第 17 行，将 add 函数包装为反射值对象。
*   第 20 行，将 10 和 20 两个整型值使用 reflect.ValueOf 包装为 reflect.Value，再将反射值对象的切片 []reflect.Value 作为函数的参数。
*   第 23 行，使用 funcValue 函数值对象的 Call() 方法，传入参数列表 paramList 调用 add() 函数。
*   第 26 行，调用成功后，通过 retList[0] 取返回值的第一个参数，使用 Int 取返回值的整数值。

#### 提示

反射调用函数的过程需要构造大量的 reflect.Value 和中间变量，对函数参数值进行逐一检查，还需要将调用参数复制到调用函数的参数内存中。调用完毕后，还需要将返回值转换为 reflect.Value，用户还需要从中取出调用值。因此，反射调用函数的性能问题尤为突出，不建议大量使用反射函数调用。