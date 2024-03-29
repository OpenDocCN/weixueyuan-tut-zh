# Go 语言变量的声明（使用 var 关键字）

变量（Variable）的功能是存储用户的数据。不同的逻辑有不同的对象类型，也就有不同的变量类型。经过半个多世纪的发展，编程语言已经形成一套固定的类型，这些类型在不同的编程语言中基本是相通的。常见变量的数据类型有：整型、浮点型、布尔型、结构体等。

Go 语言作为 C 语言家族的新派代表，在 C 语言的定义方法和类型上做了优化和调整，更加灵活易学。

Go 语言的每一个变量都拥有自己的类型，必须经过声明才能开始用。

下面先通过一段代码了解变量声明的基本样式。

```
var a int
var b string
var c []float32
var d func() bool
var e struct{
    x int
}
```

代码说明如下：

*   第 1 行，声明一个整型类型的变量，可以保存整数数值。
*   第 2 行，声明一个字符串类型的变量。
*   第 3 行，声明一个 32 位浮点切片类型的变量，浮点切片表示由多个浮点类型组成的数据结构。
*   第 4 行，声明一个返回值为布尔类型的函数变量，这种形式一般用于回调函数，即将函数以变量的形式保存下来，在需要的时候重新调用这个函数。
*   第 5 行，声明一个结构体类型的变量，这个结构体拥有一个整型的 x 字段。

上面代码的共性是，以 **var** 关键字开头，要声明的变量名放在中间，而将其类型放在后面。

变量的声明有几种形式，通过下面几节进行整理归纳。

## 标准格式

Go 语言的变量声明格式为：

var 变量名 变量类型

变量声明以关键字 var 开头，后置变量类型，行尾无须分号。

## 批量格式

觉得每行都用 var 声明变量比较烦琐？没关系，还有一种为懒人提供的定义变量的方法：

```
var (
    a int
    b string
    c []float32
    d func() bool
    e struct {
        x int
    }
)
```

使用关键字 var 和括号，可以将一组变量定义放在一起。