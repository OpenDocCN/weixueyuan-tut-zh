# Go 语言内嵌结构体成员名字冲突

嵌入结构体内部可能拥有相同的成员名，成员重名时会发生什么？下面通过例子来讲解。

```
package main

import (
    "fmt"
)

type A struct {
    a int
}

type B struct {
    a int
}

type C struct {
    A
    B
}

func main() {
    c := &C{}
    c.A.a = 1
    fmt.Println(c)
}
```

代码说明如下：

*   第 7 行和第 11 行分别定义了两个拥有 a int 字段的结构体。
*   第 15 行的结构体嵌入了 A 和 B 的结构体。
*   第 21 行实例化 C 结构体。
*   第 22 行按常规的方法，访问嵌入结构体 A 中的 a 字段，并赋值 1。
*   第 23 行可以正常输出实例化 C 结构体。

接着，将第 22 行修改为如下代码：

```
func main() {
    c := &C{}
    c.a = 1
    fmt.Println(c)
}
```

此时再编译运行，编译器报错：

.\main.go:22:3: ambiguous selector c.a

编译器告知 C 的选择器 a 引起歧义，也就是说，编译器无法决定将 1 赋给 C 中的 A 还是 B 里的字段 a。

在使用内嵌结构体时，Go 语言的编译器会非常智能地提醒我们可能发生的歧义和错误。