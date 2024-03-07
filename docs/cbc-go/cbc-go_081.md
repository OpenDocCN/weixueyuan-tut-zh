# Go 语言接口和类型之间的转换

Go 语言中使用接口断言（type assertions）将接口转换成另外一个接口，也可以将接口转换为另外的类型。接口的转换在开发中非常常见，使用也非常频繁。

## 类型断言的格式

类型断言的基本格式如下：

```
t := i.(T)
```

其中，i 代表接口变量，T 代表转换的目标类型，t 代表转换后的变量。

如果 i 没有完全实现 T 接口的方法，这个语句将会触发宕机。触发宕机不是很友好，因此上面的语句还有一种写法：

```
t,ok := i.(T)
```

这种写法下，如果发生接口未实现时，将会把 ok 置为 false，t 置为 T 类型的 0 值。正常实现时，ok 为 true。这里 ok 可以被认为是：i 接口是否实现 T 类型的结果。

## 将接口转换为其他接口

实现某个接口的类型同时实现了另外一个接口，此时可以在两个接口间转换。

鸟和猪具有不同的特性，鸟可以飞，猪不能飞，但两种动物都可以行走。如果使用结构体实现鸟和猪，让它们具备自己特性的 Fly() 和 Walk() 方法就让鸟和猪各自实现了飞行动物接口（Flyer）和行走动物接口（Walker）。

将鸟和猪的实例创建后，被保存到 interface{} 类型的 map 中。interface{} 类型表示空接口，意思就是这种接口可以保存为任意类型。对保存有鸟或猪的实例的 interface{} 变量进行断言操作，如果断言对象是断言指定的类型，则返回转换为断言对象类型的接口；如果不是指定的断言类型时，断言的第二个参数将返回 false。例如下面的代码：

```
var obj interface = new(bird)
f, isFlyer := obj.(Flyer)
```

代码中，new(bird) 产生 *bird 类型的 bird 实例，这个实例被保存在 interface{} 类型的 obj 变量中。使用 obj.(Flyer) 类型断言，将 obj 转换为 Flyer 接口。f 为转换成功时的 Flyer 接口类型，isFlyer 表示是否转换成功，类型就是 bool。

下面是详细的代码（代码 1）：

```
package main

import "fmt"

// 定义飞行动物接口
type Flyer interface {
    Fly()
}

// 定义行走动物接口
type Walker interface {
    Walk()
}

// 定义鸟类
type bird struct {
}

// 实现飞行动物接口
func (b *bird) Fly() {
    fmt.Println("bird: fly")
}

// 为鸟添加 Walk()方法, 实现行走动物接口
func (b *bird) Walk() {
    fmt.Println("bird: walk")
}

// 定义猪
type pig struct {
}

// 为猪添加 Walk()方法, 实现行走动物接口
func (p *pig) Walk() {
    fmt.Println("pig: walk")
}

func main() {

// 创建动物的名字到实例的映射
    animals := map[string]interface{}{
        "bird": new(bird),
        "pig":  new(pig),
    }

    // 遍历映射
    for name, obj := range animals {

        // 判断对象是否为飞行动物
        f, isFlyer := obj.(Flyer)
        // 判断对象是否为行走动物
        w, isWalker := obj.(Walker)

        fmt.Printf("name: %s isFlyer: %v isWalker: %v\n", name, isFlyer, isWalker)

        // 如果是飞行动物则调用飞行动物接口
        if isFlyer {
            f.Fly()
        }

        // 如果是行走动物则调用行走动物接口
        if isWalker {
            w.Walk()
        }
    }
}
```

代码说明如下：

*   第 6 行定义了飞行动物的接口。
*   第 11 行定义了行走动物的接口。
*   第 16 和 30 行分别定义了鸟和猪两个对象，并分别实现了飞行动物和行走动物接口。
*   第 41 行是一个 map，映射对象名字和对象实例，实例是鸟和猪。
*   第 47 行开始遍历 map，obj 为 interface{} 接口类型。
*   第 50 行中，使用类型断言获得 f，类型为 Flyer 及 isFlyer 的断言成功的判定。
*   第 52 行中，使用类型断言获得 w，类型为 Walker 及 isWalker 的断言成功的判定。
*   第 57 和 62 行，根据飞行动物和行走动物两者是否断言成功，调用其接口。

代码输出如下：
name: pig isFlyer: false isWalker: true
pig: walk
name: bird isFlyer: true isWalker: true
bird: fly
bird: walk

## 将接口转换为其他类型

在代码 1 中，可以实现将接口转换为普通的指针类型。例如将 Walker 接口转换为 *pig 类型，请参考下面的代码：

```
p1 := new(pig)

var a Walker = p1
p2 := a.(*pig)

fmt.Printf("p1=%p p2=%p", p1, p2)
```

对代码的说明如下：

*   第 3 行，由于 pig 实现了 Walker 接口，因此可以被隐式转换为 Walker 接口类型保存于 a 中。
*   第 4 行，由于 a 中保存的本来就是 *pig 本体，因此可以转换为 *pig 类型。
*   第 6 行，对比发现，p1 和 p2 指针是相同的。

如果尝试将上面这段代码中的 Walker 类型的 a 转换为 *bird 类型，将会发出运行时错误，请参考下面的代码：

```
p1 := new(pig)

var a Walker = p1
p2 := a.(*bird)
```

运行时报错：

panic: interface conversion: main.Walker is *main.pig, not *main.bird

报错意思是：接口转换时，main.Walker 接口的内部保存的是 *main.pig，而不是 *main.bird。

因此，接口在转换为其他类型时，接口内保存的实例对应的类型指针，必须是要转换的对应的类型指针。

#### 总结

接口和其他类型的转换可以在 Go 语言中自由进行，前提是已经完全实现。

接口断言类似于流程控制中的 if。但大量类型断言出现时，应使用更为高效的类型分支 switch 特性。