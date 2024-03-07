# Go 语言 switch case 语句

分支选择可以理解为一种批量的 if 语句，使用 switch 语句可方便地对大量的值进行判断。

在 Go 语言中的 switch，不仅可以基于常量进行判断，还可以基于表达式进行判断。

#### 提示

C/C++ 语言中的 switch 语句只能支持数值常量，不能对字符串、表达式等复杂情况进行处理，这么设计的主要原因是性能。C/C++ 的 switch 可以根据 case 的值作为偏移量直接跳转代码，在性能敏感代码处，这样做显然是有好处的。

到了 Go 语言的时代，语言的运行效率并不能直接决定最终的效率，I/O 效率现在是最主要的问题。因此，Go 语言中的 switch 语法设计尽量以使用方便为主。

## 基本写法

Go 语言改进了 switch 的语法设计，避免人为造成失误。Go 语言的 switch 中的每一个 case 与 case 间是独立的代码块，不需要通过 break 语句跳出当前 case 代码块以避免执行到下一行。示例代码如下：

```
var a = "hello"
switch a {
case "hello":
    fmt.Println(1)
case "world":
    fmt.Println(2)
default:
    fmt.Println(0)
}
```

代码输出如下：
1

上面例子中，每一个 case 均是字符串格式，且使用了 default 分支，Go 语言规定每个 switch 只能有一个 default 分支。

#### 1) 一分支多值

当出现多个 case 要放在一起的时候，可以像下面代码这样写：

```
var a = "mum"
switch a {
case "mum", "daddy":
    fmt.Println("family")
}
```

不同的 case 表达式使用逗号分隔。

#### 2) 分支表达式

case 后不仅仅只是常量，还可以和 if 一样添加表达式，代码如下：

```
var r int = 11
switch {
case r > 10 && r < 20:
    fmt.Println(r)
}
```

注意，这种情况的 switch 后面不再跟判断变量，连判断的目标都没有了。

## 跨越 case 的 fallthrough——兼容 C 语言的 case 设计

在 Go 语言中 case 是一个独立的代码块，执行完毕后不会像 C 语言那样紧接着下一个 case 执行。但是为了兼容一些移植代码，依然加入了 fallthrough 关键字来实现这一功能，代码如下：

```
var s = "hello"
switch {
case s == "hello":
    fmt.Println("hello")
    fallthrough
case s != "world":
    fmt.Println("world")
}
```

代码输出如下：
hello
world

新编写的代码，不建议使用 fallthrough。