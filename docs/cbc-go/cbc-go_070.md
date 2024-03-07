# Go 语言结构体内嵌模拟类的继承

在面向对象思想中，实现对象关系需要使用“继承”特性。例如，人类不能飞行，鸟类可以飞行。人类和鸟类都可以继承自可行走类，但只有鸟类继承自飞行类。

面向对象的设计原则中也建议对象最好不要使用多重继承，有些面向对象语言从语言层面就禁止了多重继承，如 C# 和 Java 语言。鸟类同时继承自可行走类和飞行类，这显然是存在问题的。在面向对象思想中要正确地实现对象的多重特性，只能使用一些精巧的设计来补救。

Go 语言的结构体内嵌特性就是一种组合特性，使用组合特性可以快速构建对象的不同特性。

下面的代码使用 Go 语言的结构体内嵌实现对象特性组合，请参考下面的代码。

人和鸟的特性：

```
package main

import "fmt"

// 可飞行的
type Flying struct{}

func (f *Flying) Fly() {
    fmt.Println("can fly")
}

// 可行走的
type Walkable struct{}

func (f *Walkable) Walk() {
    fmt.Println("can calk")
}

// 人类
type Human struct {
    Walkable // 人类能行走
}

// 鸟类
type Bird struct {
    Walkable // 鸟类能行走
    Flying   // 鸟类能飞行
}

func main() {

    // 实例化鸟类
    b := new(Bird)
    fmt.Println("Bird: ")
    b.Fly()
    b.Walk()

    // 实例化人类
    h := new(Human)
    fmt.Println("Human: ")
    h.Walk()

}
```

代码说明如下：

*   第 6 行，声明可飞行结构（Flying）。
*   第 8 行，为可飞行结构添加飞行方法 Fly()。
*   第 13 行，声明可行走结构（Walkable）。
*   第 15 行，为可行走结构添加行走方法 Walk()。
*   第 20 行，声明人类结构。这个结构嵌入可行走结构（Walkable），让人类具备“可行走”特性
*   第 25 行，声明鸟类结构。这个结构嵌入可行走结构（Walkable）和可飞行结构（Flying），让鸟类具备既可行走又可飞行的特性。
*   第 33 行，实例化鸟类结构。
*   第 35 和 36 行，调用鸟类可以使用的功能，如飞行和行走。
*   第 39 行，实例化人类结构。
*   第 41 行，调用人类能使用的功能，如行走。

运行代码，输出如下：
Bird:
can fly
can calk
Human:
can calk

使用 Go 语言的内嵌结构体实现对象特性，可以自由地在对象中增、删、改各种特性。Go 语言会在编译时检查能否使用这些特性。