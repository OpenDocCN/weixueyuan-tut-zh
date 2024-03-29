# Go 语言匿名变量（没有名字的变量）

在使用多重赋值时，如果不需要在左值中接收变量，可以使用匿名变量（anonymous variable）。

匿名变量的表现是一个下画线`_`，使用匿名变量时，只需要在变量声明的地方使用下画线替换即可。例如：

```
func GetData() (int, int) {
    return 100, 200
}

a, _ := GetData()
_, b := GetData()

fmt.Println(a, b)
```

代码运行结果：

100 200

GetData() 是一个函数，拥有两个整型返回值。每次调用将会返回 100 和 200 两个数值。

代码说明如下：

*   第 5 行只需要获取第一个返回值，所以将第二个返回值的变量设为下画线。
*   第 6 行将第一个返回值的变量设为匿名。

匿名变量不占用命名空间，不会分配内存。匿名变量与匿名变量之间也不会因为多次声明而无法使用。

提示：在 Lua 等编程语言里，匿名变量也被叫做哑元变量。