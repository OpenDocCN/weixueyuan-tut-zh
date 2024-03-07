# Go 语言 bool 类型（布尔类型）

> 原文：[`www.weixueyuan.net/a/466.html`](http://www.weixueyuan.net/a/466.html)

Go 语言中的布尔类型与 PHP 中的布尔类型差不多，关键字都是 bool，但是 Go 语言中布尔类型的值只能是预定义常量 true 或 false。

在 Go 语言中布尔类型的声明方式如下所示：

var v1 bool
v1 = true
v2 := (1 == 2)      // v2 也会被推导为 bool 类型

与 PHP 不同的是，Go 语言是强类型语言，变量类型一旦确定，就不能将其他类型的值赋值给该变量。因此，布尔类型不能接受其他类型的赋值，也不支持自动或强制的类型转换。以下的示例是一些错误的用法，会导致编译错误：

var b bool
b = 1           // 编译错误
b = bool(1) // 编译错误

在 Go 语言中，以下的用法才是正确的：

var b bool
b = (1!=0)                      // 编译正确
fmt.Println("Result:", b) // 打印结果为 Result: true

此外，由于强类型的缘故，Go 语言在进行布尔值真假判断时，对值的类型有严格限制，不同类型的值不能直接使用 `==` 或 `!=` 运算符进行比较，否则就会报错，比如下面这段代码：

b := (false == 0);

在编译的时候就会报如下错误：

cannot convert 0 (type untyped number) to type bool
invalid operation: false == 0 (mismatched types bool and int)

同样，`!`运算符也不能作用于非布尔类型值。