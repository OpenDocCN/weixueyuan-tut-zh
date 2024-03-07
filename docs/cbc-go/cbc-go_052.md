# Go 语言字符串的链式处理——操作与数据分离的设计技巧

使用 SQL 语言从数据库中获取数据时，可以对原始数据进行排序（sort by）、分组（group by）和去重（distinct）等操作。SQL 将数据的操作与遍历过程作为两个部分进行隔离，这样操作和遍历过程就可以各自独立地进行设计，这就是常见的数据与操作分离的设计。

对数据的操作进行多步骤的处理被称为链式处理。本例中使用多个字符串作为数据集合，然后对每个字符串进行一系列的处理，用户可以通过系统函数或者自定义函数对链式处理中的每个环节进行自定义。

首先给出本节完整代码：

```
package main

import (
    "fmt"
    "strings"
)

// 字符串处理函数，传入字符串切片和处理链
func StringProccess(list []string, chain []func(string) string) {

    // 遍历每一个字符串
    for index, str := range list {

        // 第一个需要处理的字符串
        result := str

        // 遍历每一个处理链
        for _, proc := range chain {

            // 输入一个字符串进行处理，返回数据作为下一个处理链的输入。
            result = proc(result)
        }

        // 将结果放回切片
        list[index] = result
    }
}

// 自定义的移除前缀的处理函数
func removePrefix(str string) string {

    return strings.TrimPrefix(str, "go")
}

func main() {

    // 待处理的字符串列表
    list := []string{
        "go scanner",
        "go parser",
        "go compiler",
        "go printer",
        "go formater",
    }

    // 处理函数链
    chain := []func(string) string{
        removePrefix,
        strings.TrimSpace,
        strings.ToUpper,
    }

    // 处理字符串
    StringProccess(list, chain)

    // 输出处理好的字符串
    for _, str := range list {
        fmt.Println(str)
    }

}
```

## 1) 字符串处理函数

字符串处理函数（StringProccess）需要外部提供数据源：一个字符串切片（list[]string），另外还要提供一个链式处理函数的切片（chain[]func(string)string），链式处理切片中的一个处理函数的定义如下：

func(string)string

这种处理函数能够接受一个字符串输入，处理后输出。

strings 包中将字符串变为小写就是一种处理函数的形式，strings.ToLower() 函数能够将传入的字符串的每一个字符变为小写，strings.ToLower 定义如下：

func ToLower(s string) string

字符串处理函数（StringProccess）内部遍历每一个数据源提供的字符串，每个字符串都需要经过一系列链式处理函数处理后被重新放回切片，参见下面代码。

字符串的链式处理：

```
// 字符串处理函数, 传入字符串切片和处理链
func StringProccess(list []string, chain []func(string) string) {

    // 遍历每一个字符串
    for index, str := range list {

        // 第一个需要处理的字符串
        result := str

        // 遍历每一个处理链
        for _, proc := range chain {

            // 输入一个字符串进行处理, 返回数据作为下一个处理链的输入
            result = proc(result)
        }

        // 将结果放回切片
        list[index] = result
    }
}
```

代码说明如下：

*   第 2 行，传入字符串切片 list 作为数据源，一系列的处理函数作为 chain 处理链。
*   第 5 行，遍历字符串切片的每个字符串，依次对每个字符串进行处理。
*   第 8 行，将当前字符串保存到 result 变量中，作为第一个处理函数的参数。
*   第 11 行，遍历每一个处理函数，将字符串按顺序经过这些处理函数处理。
*   第 14 行，result 变量即是每个处理函数的输入变量，处理后的变量又会重新保存到 result 变量中。
*   第 18 行，将处理完的字符串保存回切片中。

## 2) 自定义的处理函数

处理函数可以是系统提供的处理函数，如将字符串变大写或小写，也可以使用自定义函数。本例中的字符串处理的逻辑是使用一个自定义的函数实现移除指定 go 前缀的过程，参见下面代码：

```
// 自定义的移除前缀的处理函数
func removePrefix(str string) string {
    return strings.TrimPrefix(str, "go")
}
```

此函数使用了 strings.TrimPrefix() 函数实现移除字符串的指定前缀。处理后，移除前缀的字符串结果将通过 removePrefix() 函数的返回值返回。

## 3) 字符串处理主流程

字符串处理的主流程包含以下几个步骤：

1.  准备要处理的字符串列表。
2.  准备字符串处理链。
3.  处理字符串列表。
4.  打印输出后的字符串列表。

详细流程参考下面的代码：

```
func main() {

    // 待处理的字符串列表
    list := []string{
        "go scanner",
        "go parser",
        "go compiler",
        "go printer",
        "go formater",
    }

    // 处理函数链
    chain := []func(string) string{
        removePrefix,
        strings.TrimSpace,
        strings.ToUpper,
    }

    // 处理字符串
    StringProccess(list, chain)

    // 输出处理好的字符串
    for _, str := range list {
        fmt.Println(str)
    }
}
```

代码说明如下：

*   第 4 行，定义字符串切片，字符串包含 go 前缀及空格。
*   第 13 行，准备处理每个字符串的处理链，处理的顺序与函数在切片中的位置一致。removePrefix() 为自定义的函数，功能是移除 go 前缀；移除前缀的字符串左边有一个空格，使用 strings.TrimSpace 移除，这个函数的定义刚好符合处理函数的格式：func(string)string；strings.ToUpper 用于将字符串转为大写。
*   第 20 行，传入字符串切片和字符串处理链，通过 StringProcess() 函数对字符串进行处理。
*   第 23 行，遍历字符串切片的每一个字符串，打印处理好的字符串结果。

#### 提示

链式处理器是一种常见的编程设计。Netty 是使用 Java 语言编写的一款异步事件驱动的网络应用程序框架，支持快速开发可维护的高性能的面向协议的服务器和客户端，Netty 中就有类似的链式处理器的设计。

Netty 可以使用类似的处理链对封包进行收发编码及处理。Netty 的开发者可以分为 3 种：第一种是 Netty 底层开发者，第二种是每个处理环节的开发者，第三种是业务实现者，在实际开发环节中，后两种开发者往往是同一批开发者。链式处理的开发思想将数据和操作拆分、解耦，让开发者可以根据自己的技术优势和需求，进行系统开发，同时将自己的开发成果共享给其他的开发者。