# Go 语言多个变量同时赋值

编程最简单的算法之一，莫过于变量交换。交换变量的常见算法需要一个中间变量进行变量的临时保存。用传统方法编写变量交换代码如下：

```
var a int = 100
var b int = 200
var t int

t = a
a = b
b = t

fmt.Println(a, b)
```

在计算机刚发明时，内存非常“精贵”。这种变量交换往往是非常奢侈的。于是计算机“大牛”发明了一些算法来避免使用中间变量：

```
var a int = 100
var b int = 200

a = a ^ b
b = b ^ a
a = a ^ b

fmt.Println(a, b)
```

这样的算法很多，但是都有一定的数值范围和类型要求。

到了 Go 语言时，内存不再是紧缺资源，而且写法可以更简单。使用 Go 的“多重赋值”特性，可以轻松完成变量交换的任务：

```
var a int = 100
var b int = 200

b, a = a, b

fmt.Println(a, b)
```

多重赋值时，变量的左值和右值按从左到右的顺序赋值。

多重赋值在 Go 语言的错误处理和函数返回值中会大量地使用。

例如，使用 Go 语言进行排序时就需要使用交换，代码如下：

```
type IntSlice []int

func (p IntSlice) Len() int           { return len(p) }
func (p IntSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p IntSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }
```

代码说明如下：

*   第 1 行，将 []int 声明为 IntSlice 类型。
*   第 3 行，为这个类型编写一个 Len 方法，提供切片的长度。
*   第 4 行，根据提供的 i、j 元素索引，获取元素后进行比较，返回比较结果。
*   第 5 行，根据提供的 i、j 元素索引，交换两个元素的值。