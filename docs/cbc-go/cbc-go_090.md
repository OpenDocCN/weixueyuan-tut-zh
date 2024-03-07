# Go 语言工厂模式自动注册——管理多个包的结构体

本例利用包的 init 特性，将 cls1 和 cls2 两个包注册到工厂，使用字符串创建这两个注册好的结构实例。

完整代码的结构如下：

.
└── src
    └── chapter08
        └── clsfactory
            ├── main.go
            └── base
                └── factory.go
            └── cls1
                └── reg.go
            └── cls2
                └── reg.go

> 本套教程所有源码下载地址：[`pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ`](https://pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ)    提取密码：hfyf

类工厂（具体文件：…/chapter08/clsfactory/base/factory.go）

```
package base

// 类接口
type Class interface {
    Do()
}

var (
// 保存注册好的工厂信息
    factoryByName = make(map[string]func() Class)
)

// 注册一个类生成工厂
func Register(name string, factory func() Class) {
    factoryByName[name] = factory
}

// 根据名称创建对应的类
func Create(name string) Class {
    if f, ok := factoryByName[name]; ok {
        return f()
    } else {
        panic("name not found")
    }
}
```

这个包叫 base，负责处理注册和使用工厂的基础代码，该包不会引用任何外部的包。

以下是对代码的说明：

*   第 4 行定义了“产品”：类。
*   第 10 行使用了一个 map 保存注册的工厂信息。
*   第 14 行提供给工厂方注册使用，所谓的“工厂”，就是一个定义为`func() Class`的普通函数，调用此函数，创建一个类实例，实现的工厂内部结构体会实现 Class 接口。
*   第 19 行定义通过名字创建类实例的函数，该函数会在注册好后调用。
*   第 20 行在已经注册的信息中查找名字对应的工厂函数，找到后，在第 21 行调用并返回接口。
*   第 23 行是如果创建的名字没有找到时，报错。

类 1 及注册代码（具体文件：…/chapter08/clsfactory/cls1/reg.go）

```
package cls1

import (
    "chapter08/clsfactory/base"
    "fmt"
)

// 定义类 1
type Class1 struct {
}

// 实现 Class 接口
func (c *Class1) Do() {
    fmt.Println("Class1")
}

func init() {

    // 在启动时注册类 1 工厂
    base.Register("Class1", func() base.Class {
        return new(Class1)
    })
}
```

上面的代码展示了 Class1 的工厂及产品定义过程。

*   第 9～15 行定义 Class1 结构，该结构实现了 base 中的 Class 接口。
*   第 20 行，Class1 结构的实例化过程叫 Class1 的工厂，使用 base.Register() 函数在 init() 函数被调用时与一个字符串关联，这样，方便以后通过名字重新调用该函数并创建实例。

类 2 及注册代码（具体文件：…/chapter08/clsfactory/cls2/reg.go）

```
package cls2

import (
    "chapter08/clsfactory/base"
    "fmt"
)

// 定义类 2
type Class2 struct {
}

// 实现 Class 接口
func (c *Class2) Do() {
    fmt.Println("Class2")
}

func init() {

    // 在启动时注册类 2 工厂
    base.Register("Class2", func() base.Class {
        return new(Class2)
    })
}
```

Class2 的注册与 Class1 的定义和注册过程类似。

类工程主流程（具体文件：…/chapter08/clsfactory/main.go）

```
package main

import (
    "chapter08/clsfactory/base"
    _ "chapter08/clsfactory/cls1"  // 匿名引用 cls1 包, 自动注册
    _ "chapter08/clsfactory/cls2"  // 匿名引用 cls2 包, 自动注册
)

func main() {

    // 根据字符串动态创建一个 Class1 实例
    c1 := base.Create("Class1")
    c1.Do()

    // 根据字符串动态创建一个 Class2 实例
    c2 := base.Create("Class2")
    c2.Do()

}
```

下面是对代码的说明：

*   第 5 和第 6 行使用匿名引用方法导入了 cls1 和 cls2 两个包。在 main() 函数调用前，这两个包的 init() 函数会被自动调用，从而自动注册 Class1 和 Class2。
*   第 12 和第 16 行，通过 base.Create() 方法查找字符串对应的类注册信息，调用工厂方法进行实例创建。
*   第 13 和第 17 行，调用类的方法。

执行下面的指令进行编译：

export GOPATH=/home/davy/golangbook/code
go install chapter08/clsfactory
$GOPATH/bin/clsfactory

代码输出如下：
Class1
Class2