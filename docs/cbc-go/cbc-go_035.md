# Go 语言 map 元素的删除和清空

## 使用 delete() 函数从 map 中删除键值对

使用 delete() 内建函数从 map 中删除一组键值对，delete() 函数的格式如下：

delete(map, 键)

*   map 为要删除的 map 实例。
*   键为要删除的 map 键值对中的键。

从 map 中删除一组键值对可以通过下面的代码来完成：

```
scene := make(map[string]int)

// 准备 map 数据
scene["route"] = 66
scene["brazil"] = 4
scene["china"] = 960

delete(scene, "brazil")

for k, v := range scene {
    fmt.Println(k, v)
}
```

代码输出如下：
route 66
china 960

这个例子中使用 delete() 函数将 brazil 从 scene 这个 map 中删除了。

## 清空 map 中的所有元素

有意思的是，Go 语言中并没有为 map 提供任何清空所有元素的函数、方法。清空 map 的唯一办法就是重新 make 一个新的 map。不用担心垃圾回收的效率，Go 语言中的并行垃圾回收效率比写一个清空函数高效多了。