# Go 语言 for range（键值循环）

Go 语言可以使用 for range 遍历数组、切片、字符串、map 及通道（channel）。通过 for range 遍历的返回值有一定的规律：

*   数组、切片、字符串返回索引和值。
*   map 返回键和值。
*   通道（channel）只返回通道内的值。

## 遍历数组、切片——获得索引和元素

在遍历代码中，key 和 value 分别代表切片的下标及下标对应的值。下面的代码展示如何遍历切片，数组也是类似的遍历方法：

```
for key, value := range []int{1, 2, 3, 4} {
    fmt.Printf("key:%d  value:%d\n", key, value)
}
```

代码输出如下：
key:0  value:1
key:1  value:2
key:2  value:3
key:3  value:4

## 遍历字符串——获得字符

Go 语言和其他语言类似，可以通过 for range 的组合，对字符串进行遍历，遍历时，key 和 value 分别代表字符串的索引（base0）和字符串中的每一个字符。

下面这段代码展示了如何遍历字符串：

```
var str = "hello 你好"
for key, value := range str {
    fmt.Printf("key:%d value:0x%x\n", key, value)
}
```

代码输出如下：
key:0 value:0x68
key:1 value:0x65
key:2 value:0x6c
key:3 value:0x6c
key:4 value:0x6f
key:5 value:0x20
key:6 value:0x4f60
key:9 value:0x597d

代码中的 v 变量，实际类型是 rune，实际上就是 int32，以十六进制打印出来就是字符的编码。

## 遍历 map——获得 map 的键和值

对于 map 类型来说，for range 遍历时，key 和 value 分别代表 map 的索引键 key 和索引对应的值，一般被称为 map 的键值对，因为它们总是一对一对的出现。下面的代码演示了如何遍历 map。

```
m := map[string]int{
    "hello": 100,
    "world": 200,
}

for key, value := range m {
    fmt.Println(key, value)
}
```

代码输出如下：
hello 100
world 200

#### 注意

对 map 遍历时，遍历输出的键值是无序的，如果需要有序的键值对输出，需要对结果进行排序。

## 遍历通道（channel）——接收通道数据

for range 可以遍历通道（channel），但是通道在遍历时，只输出一个值，即管道内的类型对应的数据。

下面代码为我们展示了通道的遍历：

```
c := make(chan int)

go func() {

    c <- 1
    c <- 2
    c <- 3
    close(c)
}()

for v := range c {
    fmt.Println(v)
}
```

代码说明如下：

*   第 1 行创建了一个整型类型的通道。
*   第 3 行启动了一个 goroutine，其逻辑的实现体现在第 5～8 行，实现功能是往通道中推送数据 1、2、3，然后结束并关闭通道。
*   这段 goroutine 在声明结束后，在第 9 行马上被并行执行。
*   从第 11 行开始，使用 for range 对通道 c 进行遍历，其实就是不断地从通道中取数据，直到通道被关闭。

## 在遍历中选择希望获得的变量

在使用 for range 循环遍历某个对象时，一般不会同时需要 key 或者 value，这个时候可以采用一些技巧，让代码变得更简单。下面将前面的例子修改一下，参考下面的代码示例：

```
m := map[string]int{
    "hello": 100,
    "world": 200,
}

for _, value := range m {
    fmt.Println(value)
}
```

代码输出如下：
100
200

在例子中将 key 变成了下画线，那么这里的下画线就是匿名变量。什么是匿名变量？

*   可以理解为一种占位符。
*   本身这种变量不会进行空间分配，也不会占用一个变量的名字。
*   在 for range 可以对 key 使用匿名变量，也可以对 value 使用匿名变量。

再看一个匿名变量的例子：

```
for key, _ := range []int{1, 2, 3, 4} {
    fmt.Printf("key:%d \n", key)
}
```

代码输出如下：
key:0
key:1
key:2
key:3

在该例子中，value 被设置为匿名变量，只使用 key，而 key 本身就是切片的索引，所以例子输出索引。

我们总结一下 for 的功能：

*   Go 语言的 for 包含初始化语句、条件表达式、结束语句，这 3 个部分均可缺省。
*   for range 支持对数组、切片、字符串、map、通道进行遍历操作。
*   在需要时，可以使用匿名变量对 for range 的变量进行选取。