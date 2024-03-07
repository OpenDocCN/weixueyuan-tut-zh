# Go 语言 bool 类型（布尔类型）

布尔型数据在 Go 语言中以 bool 类型进行声明，布尔型数据只有 true（真）和 false（假）两个值。

Go 语言中不允许将整型强制转换为布尔型，代码如下：

```
var n bool
fmt.Println(int(n) * 2)
```

编译错误，输出如下：

cannot convert n (type bool) to type int

布尔型无法参与数值运算，也无法与其他类型进行转换。