# Go 语言常量

> 原文：[`www.weixueyuan.net/a/479.html`](http://www.weixueyuan.net/a/479.html)

相对于变量，常量是恒定不变的值，如圆周率。常量是一个简单值的标识符，在程序运行时，不会被修改。另外，常量中的数据类型只可以是布尔型、数字型（整型、浮点型和复数型）和字符串。常量的定义格式如下。

const 标识符 [类型] = 值

可以省略类型说明符 [类型]，因为编译器可以根据变量的值来自动推断其类型。显式类型定义如下。

const B string = "Steven"

隐式类型定义如下：

const C = "Steven"

定义多个相同类型的常量，可以简写为如下所示的样子：

const WIDTE, HEIGHT = value1, value2

提示：常量定义后未被使用，不会在编译时报错。

## 常量用于枚举

Go 语言现阶段没有提供枚举，可以使用常量组模拟枚举。假设数字 0、1 和 2 分别代表未知性别、女性和男性。格式如下所示：

```

package main

import "fmt"

const (
    Unknown = 0
    Female  = 1
    Male    = 2
)

func main() {
    fmt.Println(Unknown, Female, Male)
}
```

运行结果如下：

0 1 2

常量组中如果不指定类型和初始值，则与上一行非空常量的值相同，如下所示。

```

package main

import "fmt"

const (
    a = 10
    b
    c
)

func main() {
    fmt.Println(a, b, c)
}
```

运行结果如下：

10 10 10

## iota

iota 是特殊常量值，是一个系统定义的可以被编译器修改的常量值。iota 只能被用在常量的赋值中，在每一个 const 关键字出现时，被重置为 0，然后每出现一个常量，iota 所代表的数值会自动增加 1。iota 可以理解成常量组中常量的计数器，不论该常量的值是什么，只要有一个常量，那么 iota 就加 1。

iota 可以被用作枚举值，示例代码如下所示：

```

package main

import "fmt"

const (
    a = iota
    b = iota
    c = iota
)

func main() {
    fmt.Println(a, b, c)
}
```

运行结果如下：

0 1 2

第一个 iota 等于 0，每当 iota 在新的一行被使用时，它的值都会自动加 1，因此 a=0，b=1，c=2。

常量组中如果不指定类型和初始值，则与上一行非空常量的值相同。所以上述的枚举可以简写，如下所示：

```

package main

import "fmt"

const (
    a = iota
    b
    c
)

func main() {
    fmt.Println(a, b, c)
}
```

运行结果如下：

0 1 2