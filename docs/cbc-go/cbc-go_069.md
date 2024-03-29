# Go 语言类型内嵌和结构体内嵌

结构体允许其成员字段在声明时没有字段名而只有类型，这种形式的字段被称为类型内嵌或匿名字段类型内嵌的写法如下：

```
type Data struct {
    int
    float32
    bool
}

ins := &Data{
    int:     10,
    float32: 3.14,
    bool:    true,
}
```

代码说明如下：

*   第 2～4 行定义结构体中的匿名字段，类型分别是整型、浮点、布尔。
*   第 8～10 行将实例化的 Data 中的字段赋初值。

类型内嵌其实仍然拥有自己的字段名，只是字段名就是其类型本身而已，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

结构体实例化后，如果匿名的字段类型为结构体，那么可以直接访问匿名结构体里的所有成员，这种方式被称为结构体内嵌。

## 声明结构体内嵌

结构体类型内嵌比普通类型内嵌的概念复杂一些，下面通过一个实例来理解。

计算机图形学中的颜色有两种类型，一种是包含红、绿、蓝三原色的基础颜色；另一种是在基础颜色之外增加透明度的颜色。透明度在颜色中叫 Alpha，范围为 0～1 之间。0 表示完全透明，1 表示不透明。使用传统的结构体字段的方法定义基础颜色和带有透明度颜色的过程代码如下：

```
package main

import (
    "fmt"
)

// 基础颜色
type BasicColor struct {
    // 红、绿、蓝三种颜色分量
    R, G, B float32
}

// 完整颜色定义
type Color struct {

    // 将基本颜色作为成员
    Basic BasicColor

    // 透明度
    Alpha float32
}

func main() {

    var c Color

    // 设置基本颜色分量
    c.Basic.R = 1
    c.Basic.G = 1
    c.Basic.B = 0

    // 设置透明度
    c.Alpha = 1

    // 显示整个结构体内容
    fmt.Printf("%+v", c)
}
```

代码输出如下：
{Basic:{R:1 G:1 B:0} Alpha:1}

对代码的说明：

*   第 8 行定义基础颜色结构，包含 3 个颜色分量，分别是红、绿、蓝，范围为 0～1。
*   第 14 行定义了完整颜色结构，包含有基础颜色和透明度。
*   第 25 行，实例化一个完整颜色结构。
*   第 28～30 行访问基础颜色并赋值。

第 28～30 行的代码需要通过 Basic 结构才能设置 R、G、B 分量，虽然合理但是写法很复杂。使用 Go 语言的结构体内嵌写法重新调整代码如下：

```
package main

import (
    "fmt"
)

type BasicColor struct {
    R, G, B float32
}

type Color struct {
    BasicColor
    Alpha float32
}

func main() {

    var c Color
    c.R = 1
    c.G = 1
    c.B = 0

    c.Alpha = 1

    fmt.Printf("%+v", c)
}
```

代码加粗部分是经过调整及修改的代码。代码第 12 行中，将 BasicColor 结构体嵌入到 Color 结构体中，BasicColor 没有字段名而只有类型，这种写法就叫做结构体内嵌。

第 19～21 行中，可以直接对 Color 的 R、G、B 成员进行设置，编译器通过 Color 的定义知道 R、G、B 成员来自 BasicColor 内嵌的结构体。

## 结构内嵌特性

Go 语言的结构体内嵌有如下特性。

#### 1) 内嵌的结构体可以直接访问其成员变量

嵌入结构体的成员，可以通过外部结构体的实例直接访问。如果结构体有多层嵌入结构体，结构体实例访问任意一级的嵌入结构体成员时都只用给出字段名，而无须像传统结构体字段一样，通过一层层的结构体字段访问到最终的字段。例如，ins.a.b.c 的访问可以简化为 ins.c。

#### 2) 内嵌结构体的字段名是它的类型名

内嵌结构体字段仍然可以使用详细的字段进行一层层访问，内嵌结构体的字段名就是它的类型名，代码如下：

```
var c Color
c.BasicColor.R = 1
c.BasicColor.G = 1
c.BasicColor.B = 0
```

一个结构体只能嵌入一个同类型的成员，无须担心结构体重名和错误赋值的情况，编译器在发现可能的赋值歧义时会报错。