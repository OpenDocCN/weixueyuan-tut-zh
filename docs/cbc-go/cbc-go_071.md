# Go 语言初始化内嵌结构体

结构体内嵌初始化时，将结构体内嵌的类型作为字段名像普通结构体一样进行初始化，详细实现过程请参考下面的代码。

车辆结构的组装和初始化：

```
package main

import "fmt"

// 车轮
type Wheel struct {
    Size int
}

// 引擎
type Engine struct {
    Power int    // 功率
    Type  string // 类型
}

// 车
type Car struct {
    Wheel
    Engine
}

func main() {

    c := Car{

        // 初始化轮子
        Wheel: Wheel{
            Size: 18,
        },

        // 初始化引擎
        Engine: Engine{
            Type:  "1.4T",
            Power: 143,
        },
    }

    fmt.Printf("%+v\n", c)

}
```

代码说明如下：

*   第 6 行定义车轮结构。
*   第 11 行定义引擎结构。
*   第 17 行定义车结构，由车轮和引擎结构体嵌入。
*   第 27 行，将 Car 的 Wheel 字段使用 Wheel 结构体进行初始化。
*   第 32 行，将 Car 的 Engine 字段使用 Engine 结构体进行初始化。

## 初始化内嵌匿名结构体

在前面描述车辆和引擎的例子中，有时考虑编写代码的便利性，会将结构体直接定义在嵌入的结构体中。也就是说，结构体的定义不会被外部引用到。在初始化这个被嵌入的结构体时，就需要再次声明结构才能赋予数据。具体请参考下面的代码。

```
package main

import "fmt"

// 车轮
type Wheel struct {
    Size int
}

// 车
type Car struct {
    Wheel
    // 引擎
    Engine struct {
        Power int    // 功率
        Type  string // 类型
    }
}

func main() {

    c := Car{

        // 初始化轮子
        Wheel: Wheel{
            Size: 18,
        },

        // 初始化引擎
        Engine: struct {
            Power int
            Type  string
        }{
            Type:  "1.4T",
            Power: 143,
        },
    }

    fmt.Printf("%+v\n", c)

}
```

代码说明如下：

*   第 14 行中原来的 Engine 结构体被直接定义在 Car 的结构体中。这种嵌入的写法就是将原来的结构体类型转换为 struct{…}。
*   第 30 行，需要对 Car 的 Engine 字段进行初始化，由于 Engine 字段的类型并没有被单独定义，因此在初始化其字段时需要先填写 struct{…} 声明其类型。
*   第 3 行开始填充这个匿名结构体的数据，按“键：值”格式填充。