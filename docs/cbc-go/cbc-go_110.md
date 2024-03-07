# Go 语言 reflect.Elem()——通过反射获取指针指向的元素类型

Go 程序中对指针获取反射对象时，可以通过 reflect.Elem() 方法获取这个指针指向的元素类型。这个获取过程被称为取元素，等效于对指针类型变量做了一个`*`操作，代码如下：

```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    // 声明一个空结构体
    type cat struct {
    }

    // 创建 cat 的实例
    ins := &cat{}

    // 获取结构体实例的反射类型对象
    typeOfCat := reflect.TypeOf(ins)

    // 显示反射类型对象的名称和种类
    fmt.Printf("name:'%v' kind:'%v'\n",typeOfCat.Name(), typeOfCat.Kind())

    // 取类型的元素
    typeOfCat = typeOfCat.Elem()

    // 显示反射类型对象的名称和种类
    fmt.Printf("element name: '%v', element kind: '%v'\n", typeOfCat.Name(), typeOfCat.Kind())

}
```

代码输出如下：
name: ''  kind: 'ptr'
element name: 'cat', element kind: 'struct'

代码说明如下：

*   第 15 行，创建了 cat 结构体的实例，ins 是一个 *cat 类型的指针变量。
*   第 18 行，对指针变量获取反射类型信息。
*   第 21 行，输出指针变量的类型名称和种类。Go 语言的反射中对所有指针变量的种类都是 Ptr，但注意，指针变量的类型名称是空，不是 *cat。
*   第 24 行，取指针类型的元素类型，也就是 cat 类型。这个操作不可逆，不可以通过一个非指针类型获取它的指针类型。
*   第 27 行，输出指针变量指向元素的类型名称和种类，得到了 cat 的类型名称（cat）和种类（struct）。