# Go 语言类型分支（switch 判断空接口中变量的类型）

Go 语言的 switch 不仅可以像其他语言一样实现数值、字符串的判断，还有一种特殊的用途——判断一个接口内保存或实现的类型。

## 类型断言的书写格式

switch 实现类型分支时的写法格式如下：

```
switch 接口变量.(type) {
    case 类型 1:
        // 变量是类型 1 时的处理
    case 类型 2:
        // 变量是类型 2 时的处理
    …
    default:
        // 变量不是所有 case 中列举的类型时的处理
}
```

对各个部分的说明：

*   接口变量：表示需要判断的接口类型的变量。
*   类型 1、类型 2……：表示接口变量可能具有的类型列表，满足时，会指定 case 对应的分支进行处理。

## 使用类型分支判断基本类型

下面的例子将一个 interface{} 类型的参数传给 printType() 函数，通过 switch 判断 v 的类型，然后打印对应类型的提示，代码如下：

```
package main

import (
    "fmt"
)

func printType(v interface{}) {

    switch v.(type) {
    case int:
        fmt.Println(v, "is int")
    case string:
        fmt.Println(v, "is string")
    case bool:
        fmt.Println(v, "is bool")
    }
}

func main() {
    printType(1024)
    printType("pig")
    printType(true)
}
```

代码输出如下：
1024 is int
pig is string
true is bool

代码第 9 行中，v.(type) 就是类型分支的典型写法。通过这个写法，在 switch 的每个 case 中写的将是各种类型分支。

代码经过 switch 时，会判断 v 这个 interface{} 的具体类型从而进行类型分支跳转。

switch 的 default 也是可以使用的，功能和其他的 switch 一致。

## 使用类型分支判断接口类型

多个接口进行类型断言时，可以使用类型分支简化判断过程。

现在电子支付逐渐成为人们普遍使用的支付方式，电子支付相比现金支付具备很多优点。例如，电子支付能够刷脸支付，而现金支付容易被偷等。使用类型分支可以方便地判断一种支付方法具备哪些特性，具体请参考下面的代码。

电子支付和现金支付：

```
package main

import "fmt"

// 电子支付方式
type Alipay struct {
}

// 为 Alipay 添加 CanUseFaceID()方法, 表示电子支付方式支持刷脸
func (a *Alipay) CanUseFaceID() {
}

// 现金支付方式
type Cash struct {
}

// 为 Cash 添加 Stolen()方法, 表示现金支付方式会出现偷窃情况
func (a *Cash) Stolen() {
}

// 具备刷脸特性的接口
type CantainCanUseFaceID interface {
    CanUseFaceID()
}

// 具备被偷特性的接口
type ContainStolen interface {
    Stolen()
}

// 打印支付方式具备的特点
func print(payMethod interface{}) {
    switch payMethod.(type) {
    case CantainCanUseFaceID:  // 可以刷脸
        fmt.Printf("%T can use faceid\n", payMethod)
    case ContainStolen:  // 可能被偷
        fmt.Printf("%T may be stolen\n", payMethod)
    }
}

func main() {

    // 使用电子支付判断
    print(new(Alipay))

    // 使用现金判断
    print(new(Cash))
}
```

代码说明如下：

*   第 6～19 行，分别定义 Alipay 和 Cash 结构，并为它们添加具备各自特点的方法。
*   第 22～29 行，定义两种特性，即刷脸和被偷。
*   第 32 行，传入支付方式的接口。
*   第 33 行，使用类型分支进行支付方法的特性判断。
*   第 34～37 行，分别对刷脸和被偷的特性进行打印。

运行代码，输出如下：
*main.Alipay can use faceid
*main.Cash may be stolen