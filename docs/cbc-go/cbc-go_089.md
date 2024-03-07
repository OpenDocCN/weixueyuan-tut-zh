# Go 语言 import 导入包——在代码中使用其他的代码

要引用其他包的标识符，可以使用 import 关键字，导入的包名使用双引号包围，包名是从 GOPATH 开始计算的路径，使用`/`进行路径分隔。

## 默认导入的写法

导入有两种基本格式，即单行导入和多行导入，两种导入方法的导入代码效果是一致的。

#### 1) 单行导入

单行导入格式如下：

import "包 1"
import "包 2"

#### 2) 多行导入

当多行导入时，包名在 import 中的顺序不影响导入效果，格式如下：

import(
    "包 1"
    "包 2"
    …
)

参考代码 8-1 的例子来理解 import 的机制。

> 本套教程所有源码下载地址：[`pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ`](https://pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ)    提取密码：hfyf

代码 8-1 的目录层次如下：

.
└── src
    └── chapter08
        └── importadd
            ├── main.go
            └── mylib
                └── add.go

代码 8-1　加函数（具体文件：…/chapter08/importadd/mylib/add.go）

```
package mylib

func Add(a, b int) int {
    return a + b
}
```

第 3 行中的 Add() 函数以大写 A 开头，表示将 Add() 函数导出供包外使用。当首字母小写时，为包内使用，包外无法引用到。

add.go 在 mylib 文件夹下，习惯上将文件夹的命名与包名一致，命名为 mylib 包。

代码 8-2　导入包（具体文件：…/chapter08/importadd/main.go）

```
package main

import (
    "chapter08/importadd/mylib"
    "fmt"
)

func main() {
    fmt.Println(mylib.Add(1, 2))
}
```

代码说明如下：

*   第 4 行，导入 chapter08/importadd/mylib 包。
*   第 9 行，使用 mylib 作为包名，并引用 Add() 函数调用。

在命令行中运行下面代码：

export GOPATH=/home/davy/golangbook/code
go install chapter08/importadd
$GOPATH/bin/importadd

命令说明如下：

*   第 1 行，根据你的 GOPATH 不同，设置 GOPATH。
*   第 2 行，使用 go install 指令编译并安装 chapter08/code8-1 到 GOPATH 的 bin 目录下。
*   第 3 行，执行 GOPATH 的 bin 目录下的可执行文件 code8-1。

运行代码，输出结果如下：
3

## 导入包后自定义引用的包名

在默认导入包的基础上，在导入包路径前添加标识符即可形成自定义引用包，格式如下：

customName "path/to/package"

其中，path/to/package 为要导入的包路径，customName 为自定义的包名。

在 code8-1 的基础上，在 mylib 导入的包名前添加一个标识符，代码如下：

```
package main

import (
    renameLib "chapter08/importadd/mylib"
    "fmt"
)

func main() {
    fmt.Println(renameLib.Add(1, 2))
}
```

代码说明如下：

*   第 4 行，将 chapter08/importadd/mylib 包导入，并且使用 renameLib 进行引用。
*   第 9 行，使用 renameLib 调用 chapter08/importadd/mylib 包中的 Add() 函数。

## 匿名导入包——只导入包但不使用包内类型和数值

如果只希望导入包，而不使用任何包内的结构和类型，也不调用包内的任何函数时，可以使用匿名导入包，格式如下：

```
import (
    _ "path/to/package"
)
```

其中，path/to/package 表示要导入的包名，下画线`_`表示匿名导入包。

匿名导入的包与其他方式导入包一样会让导入包编译到可执行文件中，同时，导入包也会触发 init() 函数调用。

## 包在程序启动前的初始化入口：init

在某些需求的设计上需要在程序启动时统一调用程序引用到的所有包的初始化函数，如果需要通过开发者手动调用这些初始化函数，那么这个过程可能会发生错误或者遗漏。我们希望在被引用的包内部，由包的编写者获得代码启动的通知，在程序启动时做一些自己包内代码的初始化工作。

例如，为了提高数学库计算三角函数的执行效率，可以在程序启动时，将三角函数的值提前在内存中建成索引表，外部程序通过查表的方式迅速获得三角函数的值。但是三角函数索引表的初始化函数的调用不希望由每一个外部使用三角函数的开发者调用，如果在三角函数的包内有一个机制可以告诉三角函数包程序何时启动，那么就可以解决初始化的问题。

Go 语言为以上问题提供了一个非常方便的特性：init() 函数。

init() 函数的特性如下：

*   每个源码可以使用 1 个 init() 函数。
*   init() 函数会在程序执行前（main() 函数执行前）被自动调用。
*   调用顺序为 main() 中引用的包，以深度优先顺序初始化。

例如，假设有这样的包引用关系：main→A→B→C，那么这些包的 init() 函数调用顺序为：

C.init→B.init→A.init→main

说明：

*   同一个包中的多个 init() 函数的调用顺序不可预期。
*   init() 函数不能被其他函数调用。

## 理解包导入后的 init()函数初始化顺序

Go 语言包会从 main 包开始检查其引用的所有包，每个包也可能包含其他的包。Go 编译器由此构建出一个树状的包引用关系，再根据引用顺序决定编译顺序，依次编译这些包的代码。

在运行时，被最后导入的包会最先初始化并调用 init() 函数。

通过下面的代码理解包的初始化顺序。

代码 8-3　包导入初始化顺序入口（…/chapter08/pkginit/main.go）

```
package main

import "chapter08/code8-2/pkg1"

func main() {

    pkg1.ExecPkg1()
}
```

代码说明如下：

*   第 3 行，导入 pkg1 包。
*   第 7 行，调用 pkg1 包的 ExecPkg1() 函数。

代码 8-4　包导入初始化顺序 pkg1（…/chapter08/pkginit/pkg1/pkg1.go）

```
package pkg1

import (
    "chapter08/code8-2/pkg2"
    "fmt"
)

func ExecPkg1() {

    fmt.Println("ExecPkg1")

    pkg2.ExecPkg2()
}

func init() {
    fmt.Println("pkg1 init")
}
```

代码说明如下：

*   第 4 行，导入 pkg2 包。
*   第 8 行，声明 ExecPkg1() 函数。
*   第 12 行，调用 pkg2 包的 ExecPkg2() 函数。
*   第 15 行，在 pkg1 包初始化时，打印 pkg1 init。

代码 8-5　包导入初始化顺序 pkg2（…/chapter08/pkginit/pkg2/pkg2.go）

```
package pkg2

import "fmt"

func ExecPkg2() {
    fmt.Println("ExecPkg2")
}

func init() {
    fmt.Println("pkg2 init")
}
```

代码说明如下：

*   第 5 行，声明 ExecPkg2() 函数。
*   第 10 行，在 pkg2 包初始化时，打印 pkg2 init。

执行代码，输出如下：
pkg2 init
pkg1 init
ExecPkg1
ExecPkg2