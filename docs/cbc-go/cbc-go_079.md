# Go 语言排序（借助 sort.Interface 接口）

排序是常见的算法之一，也是常见的面试题之一，程序员对各种排序算法也是津津乐道。实际使用中，语言的类库会为我们提供健壮、高性能的排序算法库，开发者在了解排序算法基本原理的基础上，应该避免“造轮子”，直接使用已有的排序算法库，以缩短开发周期，提高开发效率。

Go 语言中在排序时，需要使用者通过 **sort.Interface** 接口提供数据的一些特性和操作方法。接口定义代码如下：

```
type Interface interface {
    // 获取元素数量
    Len() int

    // 小于比较
    Less(i, j int) bool

    // 交换元素
    Swap(i, j int)
}
```

代码说明如下：

*   第 3 行，排序算法需要实现者提供需要排序的数据元素数量。
*   第 6 行，排序需要通过比较元素之间的关系才能做出具体的操作。Less() 方法需要提供两个给定索引（i 和 j）对应元素的小于比较（数值的 < 操作）的结果。参数的 i、j 传入的是元素的索引。将传入的 i、j 索引对应的元素按小于关系进行比较，完成后把结果通过 Less() 方法的返回值返回。
*   第 9 行，排序的过程就是不停地交换元素。Swap() 方法需要实现者通过传入 i、j 索引找到元素，并交换元素的值。

这个接口需要实现者实现的方法就是排序的经典操作：数量（Len）、比较（Less）、交换（Swap）。

## 使用 sort.Interface 接口进行排序

对一系列字符串进行排序时，使用字符串切片（[]string）承载多个字符串。使用 type 关键字，将字符串切片（[]string）定义为自定义类型 MyStringList。为了让 sort 包能识别 MyStringList，能够对 MyStringList 进行排序，就必须让 MyStringList 实现 sort.Interface 接口。

下面是对字符串排序的详细代码（代码 1）：

```
package main

import (
    "fmt"
    "sort"
)

// 将[]string 定义为 MyStringList 类型
type MyStringList []string

// 实现 sort.Interface 接口的获取元素数量方法
func (m MyStringList) Len() int {
    return len(m)
}

// 实现 sort.Interface 接口的比较元素方法
func (m MyStringList) Less(i, j int) bool {
    return m[i] < m[j]
}

// 实现 sort.Interface 接口的交换元素方法
func (m MyStringList) Swap(i, j int) {
    m[i], m[j] = m[j], m[i]
}

func main() {

    // 准备一个内容被打乱顺序的字符串切片
    names := MyStringList{
        "3\. Triple Kill",
        "5\. Penta Kill",
        "2\. Double Kill",
        "4\. Quadra Kill",
        "1\. First Blood",
    }

    // 使用 sort 包进行排序
    sort.Sort(names)

    // 遍历打印结果
    for _, v := range names {
            fmt.Printf("%s\n", v)
    }

}
```

代码输出结果：
1\. First Blood
2\. Double Kill
3\. Triple Kill
4\. Quadra Kill
5\. Penta Kill

代码说明如下：

*   第 9 行，接口实现不受限于结构体，任何类型都可以实现接口。要排序的字符串切片 []string 是系统定制好的类型，无法让这个类型去实现 sort.Interface 排序接口。因此，需要将 []string 定义为自定义的类型。
*   第 12 行，实现获取元素数量的 Len() 方法，返回字符串切片的元素数量。
*   第 17 行，实现比较元素的 Less() 方法，直接取 m 切片的 i 和 j 元素值进行小于比较，并返回比较结果。
*   第 22 行，实现交换元素的 Swap() 方法，这里使用 Go 语言的多变量赋值特性实现元素交换。
*   第 29 行，由于将 []string 定义成 MyStringList 类型，字符串切片初始化的过程等效于下面的写法：

    ```
    names := []string {
        "3\. Triple Kill",
        "5\. Penta Kill",
        "2\. Double Kill",
        "4\. Quadra Kill",
        "1\. First Blood",
    }
    ```

*   第 38 行，使用 sort 包的 Sort() 函数，将 names（MyStringList 类型）进行排序。排序时，sort 包会通过 MyStringList 实现的 Len()、Less()、Swap() 这 3 个方法进行数据获取和修改。
*   第 41 行，遍历排序好的字符串切片，并打印结果。

## 常见类型的便捷排序

通过实现 sort.Interface 接口的排序过程具有很强的可定制性，可以根据被排序对象比较复杂的特性进行定制。例如，需要多种排序逻辑的需求就适合使用 sort.Interface 接口进行排序。但大部分情况中，只需要对字符串、整型等进行快速排序。Go 语言中提供了一些固定模式的封装以方便开发者迅速对内容进行排序。

#### 1) 字符串切片的便捷排序

sort 包中有一个 StringSlice 类型，定义如下：

```
type StringSlice []string

func (p StringSlice) Len() int           { return len(p) }
func (p StringSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p StringSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

// Sort is a convenience method.
func (p StringSlice) Sort() { Sort(p) }
```

sort 包中的 StringSlice 的代码与 MyStringList 的实现代码几乎一样。因此，只需要使用 sort 包的 StringSlice 就可以更简单快速地进行字符串排序。将代码 1 中的排序代码简化后如下所示：

```
names := sort.StringSlice{
    "3\. Triple Kill",
    "5\. Penta Kill",
    "2\. Double Kill",
    "4\. Quadra Kill",
    "1\. First Blood",
}

sort.Sort(names)
```

简化后，只要两句代码就实现了字符串排序的功能。

#### 2) 对整型切片进行排序

除了字符串可以使用 sort 包进行便捷排序外，还可以使用 sort.IntSlice 进行整型切片的排序。sort.IntSlice 的定义如下：

```
type IntSlice []int

func (p IntSlice) Len() int           { return len(p) }
func (p IntSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p IntSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

// Sort is a convenience method.
func (p IntSlice) Sort() { Sort(p) }
```

sort 包在 sort.Interface 对各类型的封装上还有更进一步的简化，下面使用 sort.Strings 继续对代码 1 进行简化，代码如下：

```
names := []string{
    "3\. Triple Kill",
    "5\. Penta Kill",
    "2\. Double Kill",
    "4\. Quadra Kill",
    "1\. First Blood",
}

sort.Strings(names)

// 遍历打印结果
for _, v := range names {
    fmt.Printf("%s\n", v)
}
```

代码说明如下：

*   第 1 行，需要排序的字符串切片。
*   第 9 行，使用 sort.Strings 直接对字符串切片进行排序。

#### 3) sort 包内建的类型排序接口一览

Go 语言中的 sort 包中定义了一些常见类型的排序方法，如下表所示。

sort 包中内建的类型排序接口

| 类  型 | 实现 sort.lnterface 的类型 | 直接排序方法 | 说  明 |
| 字符串（String） | StringSlice | sort.Strings(a [] string) | 字符 ASCII 值升序 |
| 整型（int） | IntSlice | sort.Ints(a []int) | 数值升序 |
| 双精度浮点（float64） | Float64Slice | sort.Float64s(a []float64) | 数值升序 |

编程中经常用到的 int32、int64、float32、bool 类型并没有由 sort 包实现，使用时依然需要开发者自己编写。

## 对结构体数据进行排序

除了基本类型的排序，也可以对结构体进行排序。结构体比基本类型更为复杂，排序时不能像数值和字符串一样拥有一些固定的单一原则。结构体的多个字段在排序中可能会存在多种排序的规则，例如，结构体中的名字按字母升序排列，数值按从小到大的顺序排序。一般在多种规则同时存在时，需要确定规则的优先度，如先按名字排序，再按年龄排序等。

#### 1) 完整实现 sort.Interface 进行结构体排序

将一批英雄名单使用结构体定义，英雄名单的结构体中定义了英雄的名字和分类。排序时要求按照英雄的分类进行排序，相同分类的情况下按名字进行排序，详细代码实现过程如下。

结构体排序代码（代码 2）：

```
package main

import (
    "fmt"
    "sort"
)

// 声明英雄的分类
type HeroKind int

// 定义 HeroKind 常量, 类似于枚举
const (
    None HeroKind = iota
    Tank
    Assassin
    Mage
)

// 定义英雄名单的结构
type Hero struct {
    Name string  // 英雄的名字
    Kind HeroKind  // 英雄的种类
}

// 将英雄指针的切片定义为 Heros 类型
type Heros []*Hero

// 实现 sort.Interface 接口取元素数量方法
func (s Heros) Len() int {
    return len(s)
}

// 实现 sort.Interface 接口比较元素方法
func (s Heros) Less(i, j int) bool {

    // 如果英雄的分类不一致时, 优先对分类进行排序
    if s[i].Kind != s[j].Kind {
        return s[i].Kind < s[j].Kind
    }

    // 默认按英雄名字字符升序排列
    return s[i].Name < s[j].Name
}

// 实现 sort.Interface 接口交换元素方法
func (s Heros) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

func main() {

    // 准备英雄列表
    heros := Heros{
        &Hero{"吕布", Tank},
        &Hero{"李白", Assassin},
        &Hero{"妲己", Mage},
        &Hero{"貂蝉", Assassin},
        &Hero{"关羽", Tank},
        &Hero{"诸葛亮", Mage},
    }

    // 使用 sort 包进行排序
    sort.Sort(heros)

    // 遍历英雄列表打印排序结果
    for _, v := range heros {
        fmt.Printf("%+v\n", v)
    }
}
```

代码输出如下：
&{Name:关羽 Kind:1}
&{Name:吕布 Kind:1}
&{Name:李白 Kind:2}
&{Name:貂蝉 Kind:2}
&{Name:妲己 Kind:3}
&{Name:诸葛亮 Kind:3}

代码说明如下：

*   第 9 行，将 int 声明为 HeroKind 英雄类型，后面会将这个类型当做枚举来使用。
*   第 13 行，定义一些英雄类型常量，可以理解为枚举的值。
*   第 26 行，为了方便实现 sort.Interface 接口，将 []*Hero 定义为 Heros 类型。
*   第 29 行，Heros 类型实现了 sort.Interface 的 Len() 方法，返回英雄的数量。
*   第 34 行，Heros 类型实现了 sort.Interface 的 Less() 方法，根据英雄字段的比较结果决定如何排序。
*   第 37 行，当英雄的分类不一致时，优先按分类的枚举数值从小到大排序。
*   第 42 行，英雄分类相等的情况下，默认根据英雄的名字字符升序排序。
*   第 46 行，Heros 类型实现了 sort.Interface 的 Swap() 方法，交换英雄元素的位置。
*   第 53～60 行，准备一系列英雄数据。
*   第 63 行，使用 sort 包进行排序。
*   第 66 行，遍历所有排序完成的英雄数据。

#### 2) 使用 sort.Slice 进行切片元素排序

从 Go 1.8 开始，Go 语言在 sort 包中提供了 sort.Slice() 函数进行更为简便的排序方法。sort.Slice() 函数只要求传入需要排序的数据，以及一个排序时对元素的回调函数，类型为 func(i,j int)bool，sort.Slice() 函数的定义如下：

```
func Slice(slice interface{}, less func(i, j int) bool)
```

使用 sort.Slice() 函数，对代码 2 重新优化的完整代码如下：

```
package main

import (
    "fmt"
    "sort"
)

type HeroKind int

const (
    None = iota
    Tank
    Assassin
    Mage
)

type Hero struct {
    Name string
    Kind HeroKind
}

func main() {

    heros := []*Hero{
        {"吕布", Tank},
        {"李白", Assassin},
        {"妲己", Mage},
        {"貂蝉", Assassin},
        {"关羽", Tank},
        {"诸葛亮", Mage},
    }

    sort.Slice(heros, func(i, j int) bool {
        if heros[i].Kind != heros[j].Kind {
            return heros[i].Kind < heros[j].Kind
        }

        return heros[i].Name < heros[j].Name
    })

    for _, v := range heros {
        fmt.Printf("%+v\n", v)
    }
}
```

第 33 行到第 39 行加粗部分是新添加的 sort.Slice() 及回调函数部分。对比前面的代码，这里去掉了 Heros 及接口实现部分的代码。

使用 sort.Slice() 不仅可以完成结构体切片排序，还可以对各种切片类型进行自定义排序。