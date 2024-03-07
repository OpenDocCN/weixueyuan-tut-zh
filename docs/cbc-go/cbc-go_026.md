# Go 语言 type 关键字（类型别名）

注意：本节内容涉及 Go 语言新版本的功能。内容上会涉及后续章节讲解的类型定义及结构体嵌入等特性。另外，本节内容适用于对 Go 语言很熟悉且正在关注工程升级、代码重构等问题的读者阅读。

类型别名是 Go 1.9 版本添加的新功能。主要用于代码升级、迁移中类型的兼容性问题。在 C/C++ 语言中，代码重构升级可以使用宏快速定义新的一段代码。Go 语言中没有选择加入宏，而是将解决重构中最麻烦的类型名变更问题。

在 Go 1.9 版本之前的内建类型定义的代码是这样写的：

```
type byte uint8
type rune int32
```

而在 Go 1.9 版本之后变为：

```
type byte = uint8
type rune = int32
```

这个修改就是配合类型别名而进行的修改。

## 区分类型别名与类型定义

类型别名的写法为：

type TypeAlias = Type

类型别名规定：TypeAlias 只是 Type 的别名，本质上 TypeAlias 与 Type 是同一个类型。就像一个孩子小时候有小名、乳名，上学后用学名，英语老师又会给他起英文名，但这些名字都指的是他本人。

类型别名与类型定义表面上看只有一个等号的差异，那么它们之间实际的区别有哪些呢？下面通过一段代码来理解。

```
package main

import (
    "fmt"
)

// 将 NewInt 定义为 int 类型
type NewInt int

// 将 int 取一个别名叫 IntAlias
type IntAlias = int

func main() {

    // 将 a 声明为 NewInt 类型
    var a NewInt
    // 查看 a 的类型名
    fmt.Printf("a type: %T\n", a)

    // 将 a2 声明为 IntAlias 类型
    var a2 IntAlias
    // 查看 a2 的类型名
    fmt.Printf("a2 type: %T\n", a2)
}
```

代码运行结果：
a type: main.NewInt
a2 type: int

代码说明如下：

*   第 8 行，将 NewInt 定义为 int 类型，这是常见定义类型的方法，通过 type 关键字的定义，NewInt 会形成一种新的类型。NewInt 本身依然具备 int 的特性。
*   第 11 行，将 IntAlias 设置为 int 的一个别名，使用 IntAlias 与 int 等效。
*   第 16 行，将 a 声明为 NewInt 类型，此时若打印，则 a 的值为 0。
*   第 18 行，使用`%T`格式化参数，显示 a 变量本身的类型。
*   第 21 行，将 a2 声明为 IntAlias 类型，此时打印 a2 的值为 0。
*   第 23 行，显示 a2 变量的类型。

结果显示 a 的类型是 main.NewInt，表示 main 包下定义的 NewInt 类型。a2 类型是 int。IntAlias 类型只会在代码中存在，编译完成时，不会有 IntAlias 类型。

## 非本地类型不能定义方法

能够随意地为各种类型起名字，是否意味着可以在自己包里为这些类型任意添加方法？参见下面的代码演示：

```
package main

import (
    "time"
)

// 定义 time.Duration 的别名为 MyDuration
type MyDuration = time.Duration

// 为 MyDuration 添加一个函数
func (m MyDuration) EasySet(a string) {

}

func main() {

}
```

代码说明如下：

*   第 8 行，使用类型别名为 time.Duration 设定一个别名叫 MyDuration。
*   第 11 行，为这个别名添加一个方法。

编译上面代码报错，信息如下：

cannot define new methods on non-local type time.Duration

编译器提示：不能在一个非本地的类型 time.Duration 上定义新方法。非本地方法指的就是使用 time.Duration 的代码所在的包，也就是 main 包。因为 time.Duration 是在 time 包中定义的，在 main 包中使用。time.Duration 包与 main 包不在同一个包中，因此不能为不在一个包中的类型定义方法。

解决这个问题有下面两种方法：

*   将第 8 行修改为 type MyDuration time.Duration，也就是将 MyDuration 从别名改为类型。
*   将 MyDuration 的别名定义放在 time 包中。

## 在结构体成员嵌入时使用别名

当类型别名作为结构体嵌入的成员时会发生什么情况？请参考下面的代码。

```
package main

import (
    "fmt"
    "reflect"
)

// 定义商标结构
type Brand struct {
}

// 为商标结构添加 Show()方法
func (t Brand) Show() {
}

// 为 Brand 定义一个别名 FakeBrand
type FakeBrand = Brand

// 定义车辆结构
type Vehicle struct {

    // 嵌入两个结构
    FakeBrand
    Brand
}

func main() {

// 声明变量 a 为车辆类型
    var a Vehicle

    // 指定调用 FakeBrand 的 Show
    a.FakeBrand.Show()

    // 取 a 的类型反射对象
    ta := reflect.TypeOf(a)

    // 遍历 a 的所有成员
    for i := 0; i < ta.NumField(); i++ {

        // a 的成员信息
        f := ta.Field(i)

        // 打印成员的字段名和类型
        fmt.Printf("FieldName: %v, FieldType: %v\n", f.Name, f.Type.
            Name())
    }
}
```

代码输出如下：
FieldName: FakeBrand, FieldType: Brand
FieldName: Brand, FieldType: Brand

代码说明如下：

*   第 9 行，定义商标结构。
*   第 13 行，为商标结构添加 Show() 方法。
*   第 17 行，为 Brand 定义一个别名 FakeBrand。
*   第 20～25 行，定义车辆结构 Vehicle，嵌入 FakeBrand 和 Brand 结构。
*   第 30 行，将 Vechicle 实例化为 a。
*   第 33 行，显式调用 Vehicle 中 FakeBrand 的 Show() 方法。
*   第 36 行，使用反射取变量 a 的反射类型对象，以查看其成员类型。
*   第 39～42 行，遍历 a 的结构体成员。
*   第 45 行，打印 Vehicle 类型所有成员的信息。

这个例子中，FakeBrand 是 Brand 的一个别名。在 Vehicle 中嵌入 FakeBrand 和 Brand 并不意味着嵌入两个 Brand。FakeBrand 的类型会以名字的方式保留在 Vehicle 的成员中。

如果尝试将第 33 行改为：

a.Show()

编译器将发生报错：

ambiguous selector a.Show

在调用 Show() 方法时，因为两个类型都有 Show() 方法，会发生歧义，证明 FakeBrand 的本质确实是 Brand 类型。