# Go 语言可变参数（变参函数）

所谓可变参数，是指参数数量不固定的函数形式。Go 语言支持可变参数特性，函数声明和调用时没有固定数量的参数，同时也提供了一套方法进行可变参数的多级传递。

Go 语言的可变参数格式如下：

func 函数名(固定参数列表, v … T)(返回参数列表){
    函数体
}

以下是对可变参数的函数的说明：

*   可变参数一般被放置在函数列表的末尾，前面是固定参数列表，当没有固定参数时，所有变量就将是可变参数。
*   v 为可变参数变量，类型为 []T，也就是拥有多个 T 元素的 T 类型切片，v 和 T 之间由`...`即 3 个点组成。
*   T 为可变参数的类型，当 T 为 interface{} 时，传入的可以是任意类型。

## fmt 包中的例子

可变参数有两种形式：所有参数都是可变参数的形式，如 fmt.Println，以及部分是可变参数的形式，如 fmt.Printf，可变参数只能出现在参数的后半部分，因此不可变的参数只能放在参数的前半部分。

#### 1) 所有参数都是可变参数：fmt.Println

fmt.Println 的函数声明如下：

```
func Println(a ...interface{}) (n int, err error) {
    return Fprintln(os.Stdout, a...)
}
```

fmt.Println 在使用时，传入的值类型不受限制，例如：

```
fmt.Println(5, "hello", &struct{ a int }{1}, true)
```

#### 2) 部分参数是可变参数：fmt.Printf

fmt.Printf 的第一个参数为参数列表，后面的参数是可变参数，fmt.Printf 函数的格式如下：

```
func Printf(format string, a ...interface{}) (n int, err error) {
    return Fprintf(os.Stdout, format, a...)
}
```

fmt.Printf() 函数在调用时，第一个函数始终必须传入字符串，对应参数是 format，后面的参数数量可以变化，使用时，代码如下：

```
fmt.Printf("pure string\n")
fmt.Printf("value: %v %f\n", true, math.Pi)
```

## 遍历可变参数列表——获取每一个参数的值

可变参数列表的数量不固定，传入的参数是一个切片。如果需要获得每一个参数的具体值时，可以对可变参数变量进行遍历，参见下面代码：

```
package main

import (
    "bytes"
    "fmt"
)
// 定义一个函数, 参数数量为 0~n, 类型约束为字符串
func joinStrings(slist ...string) string {

    // 定义一个字节缓冲, 快速地连接字符串
    var b bytes.Buffer
    // 遍历可变参数列表 slist, 类型为[]string
    for _, s := range slist {
        // 将遍历出的字符串连续写入字节数组
        b.WriteString(s)
    }

    // 将连接好的字节数组转换为字符串并输出
    return b.String()
}

func main() {
    // 输入 3 个字符串, 将它们连成一个字符串
    fmt.Println(joinStrings("pig ", "and", " rat"))
    fmt.Println(joinStrings("hammer", " mom", " and", " hawk"))
}
```

代码输出如下：
pig and rat
hammer mom and hawk

代码说明如下：

*   第 8 行，定义了一个可变参数的函数，slist 的类型为 []string，每一个参数的类型是 string，也就是说，该函数只接受字符串类型作为参数。
*   第 11 行，bytes.Buffer 在这个例子中的作用类似于 StringBuilder，可以高效地进行字符串连接操作。
*   第 13 行，遍历 slist 可变参数，s 为每个参数的值，类型为 string。
*   第 15 行，将每一个传入参数放到 bytes.Buffer 中。
*   第 19 行，将 bytes.Buffer 中的数据转换为字符串作为函数返回值返回。
*   第 24 行，输入 3 个字符串，使用 joinStrings() 函数将参数连接为字符串输出。
*   第 25 行，输入 4 个字符串，连接后输出。

如果要获取可变参数的数量，可以使用 len() 函数对可变参数变量对应的切片进行求长度操作，以获得可变参数数量。

## 获得可变参数类型——获得每一个参数的类型

当可变参数为 interface{} 类型时，可以传入任何类型的值。此时，如果需要获得变量的类型，可以通过 switch 类型分支获得变量的类型。下面的代码演示将一系列不同类型的值传入 printTypeValue() 函数，该函数将分别为不同的参数打印它们的值和类型的详细描述。

打印类型及值：

```
package main

import (
    "bytes"
    "fmt"
)

func printTypeValue(slist ...interface{}) string {

    // 字节缓冲作为快速字符串连接
    var b bytes.Buffer

    // 遍历参数
    for _, s := range slist {

        // 将 interface{}类型格式化为字符串
        str := fmt.Sprintf("%v", s)

        // 类型的字符串描述
        var typeString string

        // 对 s 进行类型断言
        switch s.(type) {
        case bool:    // 当 s 为布尔类型时
            typeString = "bool"
        case string:    // 当 s 为字符串类型时
            typeString = "string"
        case int:    // 当 s 为整型类型时
            typeString = "int"
        }

        // 写值字符串前缀
        b.WriteString("value: ")

        // 写入值
        b.WriteString(str)

        // 写类型前缀
        b.WriteString(" type: ")

        // 写类型字符串
        b.WriteString(typeString)

        // 写入换行符
        b.WriteString("\n")

    }
    return b.String()
}

func main() {

    // 将不同类型的变量通过 printTypeValue()打印出来
    fmt.Println(printTypeValue(100, "str", true))
}
```

代码输出如下：
value: 100 type: int
value: str type: string
value: true type: bool

代码说明如下：

*   第 8 行，printTypeValue() 输入不同类型的值并输出类型和值描述。
*   第 11 行，bytes.Buffer 字节缓冲作为快速字符串连接。
*   第 14 行，遍历 slist 的每一个元素，类型为 interface{}。
*   第 17 行，使用 fmt.Sprintf 配合`%v`动词，可以将 interface{} 格式的任意值转为字符串。
*   第 20 行，声明一个字符串，作为变量的类型名。
*   第 23 行，switch s.(type) 可以对 interface{} 类型进行类型断言，也就是判断变量的实际类型。
*   第 24～29 行为 s 变量可能的类型，将每种类型的对应类型字符串赋值到 typeString 中。
*   第 33～42 行为写输出格式的过程。

## 在多个可变参数函数中传递参数

可变参数变量是一个包含所有参数的切片，如果要在多个可变参数中传递参数，可以在传递时在可变参数变量中默认添加`...`，将切片中的元素进行传递，而不是传递可变参数变量本身。

下面的例子模拟 print() 函数及实际调用的 rawPrint() 函数，两个函数都拥有可变参数，需要将参数从 print 传递到 rawPrint 中。

可变参数传递：

```
package main

import "fmt"

// 实际打印的函数
func rawPrint(rawList ...interface{}) {

    // 遍历可变参数切片
    for _, a := range rawList {

        // 打印参数
        fmt.Println(a)
    }
}

// 打印函数封装
func print(slist ...interface{}) {

    // 将 slist 可变参数切片完整传递给下一个函数
    rawPrint(slist...)
}

func main() {

    print(1, 2, 3)
}
```

代码输出如下：
1
2
3

对代码的说明：

*   第 9～13 行，遍历 rawPrint() 的参数列表 rawList 并打印。
*   第 20 行，将变量在 print 的可变参数列表中添加`...`后传递给 rawPrint()。
*   第 25 行，传入 1、2、3 这 3 个整型值进行打印。

如果尝试将第 20 行修改为：

```
rawPrint("fmt", slist)
```

再次执行代码，将输出：
[1 2 3]

此时，slist（类型为 []interface{}）将被作为一个整体传入 rawPrint()，rawPrint() 函数中遍历的变量也就是 slist 的切片值。

可变参数使用`...`进行传递与切片间使用 append 连接是同一个特性。