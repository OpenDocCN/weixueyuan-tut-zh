# Go 语言使用空接口实现可以保存任意值的字典

空接口可以保存任何类型这个特性可以方便地用于容器的设计。下面例子使用 map 和 interface{} 实现了一个字典。字典在其他语言中的功能和 map 类似，可以将任意类型的值做成键值对保存，然后进行找回、遍历操作。详细实现过程请参考下面的代码。

```
package main

import "fmt"

// 字典结构
type Dictionary struct {
    data map[interface{}]interface{} // 键值都为 interface{}类型
}

// 根据键获取值
func (d *Dictionary) Get(key interface{}) interface{} {
    return d.data[key]
}

// 设置键值
func (d *Dictionary) Set(key interface{}, value interface{}) {

    d.data[key] = value
}

// 遍历所有的键值，如果回调返回值为 false，停止遍历
func (d *Dictionary) Visit(callback func(k, v interface{}) bool) {

    if callback == nil {
        return
    }

    for k, v := range d.data {
        if !callback(k, v) {
            return
        }
    }
}

// 清空所有的数据
func (d *Dictionary) Clear() {
    d.data = make(map[interface{}]interface{})
}

// 创建一个字典
func NewDictionary() *Dictionary {
    d := &Dictionary{}

    // 初始化 map
    d.Clear()
    return d
}

func main() {

    // 创建字典实例
    dict := NewDictionary()

    // 添加游戏数据
    dict.Set("My Factory", 60)
    dict.Set("Terra Craft", 36)
    dict.Set("Don't Hungry", 24)

    // 获取值及打印值
    favorite := dict.Get("Terra Craft")
    fmt.Println("favorite:", favorite)

    // 遍历所有的字典元素
    dict.Visit(func(key, value interface{}) bool {

        // 将值转为 int 类型，并判断是否大于 40
        if value.(int) > 40 {

            // 输出很贵
            fmt.Println(key, "is expensive")
            return true
        }

        // 默认都是输出很便宜
        fmt.Println(key, "is cheap")

        return true
    })
}
```

## 值设置和获取

字典内部拥有一个 data 字段，其类型为 map。这个 map 的键和值都是 interface{} 类型，也就是实现任意类型关联任意类型。字典的值设置和获取通过 Set() 和 Get() 两个方法来完成，参数都是 interface{}。详细实现代码如下：

```
// 字典结构
type Dictionary struct {
    data map[interface{}]interface{}  // 键值都为 interface{}类型
}

// 根据键获取值
func (d *Dictionary) Get(key interface{}) interface{} {
    return d.data[key]
}

// 设置键值
func (d *Dictionary) Set(key interface{}, value interface{}) {
    d.data[key] = value
}
```

代码说明如下：

*   第 3 行，Dictionary 的内部实现是一个键值均为 interface{} 类型的 map，map 也具备与 Dictionary 一致的功能。
*   第 8 行，通过 map 直接获取值，如果键不存在，将返回 nil。
*   第 13 行，通过 map 设置键值。

## 遍历字段的所有键值关联数据

每个容器都有遍历操作。遍历时，需要提供一个回调返回需要遍历的数据。为了方便在必要时终止遍历操作，可以将回调的返回值设置为 bool 类型，外部逻辑在回调中不需要遍历时直接返回 false 即可终止遍历。

Dictionary 的 Visit() 方法需要传入回调函数，回调函数的类型为 func(k,v interface{})bool。每次遍历时获得的键值关联数据通过回调函数的 k 和 v 参数返回。Visit 的详细实现请参考下面的代码：

```
// 遍历所有的键值, 如果回调返回值为 false, 停止遍历
func (d *Dictionary) Visit(callback func(k, v interface{}) bool) {

    if callback == nil {
        return
    }

    for k, v := range d.data {
        if !callback(k, v) {
            return
        }
    }
}
```

代码说明如下：

*   第 2 行，定义回调，类型为 func(k,v interface{})bool，意思是返回键值数据（k、v）。bool 表示遍历流程控制，返回 true 时继续遍历，返回 false 时终止遍历。
*   第 4 行，当 callback 为空时，退出遍历，避免后续代码访问空的 callback 而导致的崩溃。
*   第 8 行，遍历字典结构的 data 成员，也就是遍历 map 的所有元素。
*   第 9 行，根据 callback 的返回值，决定是否继续遍历。

## 初始化和清除

字典结构包含有 map，需要在创建 Dictionary 实例时初始化 map。这个过程通过 Dictionary 的 Clear() 方法完成。在 NewDictionary 中调用 Clear() 方法避免了 map 初始化过程的代码重复问题。请参考下面的代码：

```
// 清空所有的数据
func (d *Dictionary) Clear() {
    d.data = make(map[interface{}]interface{})
}

// 创建一个字典
func NewDictionary() *Dictionary {
    d := &Dictionary{}

    // 初始化 map
    d.Clear()
    return d
}
```

代码说明如下：

*   第 3 行，map 没有独立的复位内部元素的操作，需要复位元素时，使用 make 创建新的实例。Go 语言的垃圾回收是并行的，不用担心 map 清除的效率问题。
*   第 7 行，实例化一个 Dictionary。
*   第 11 行，在初始化时调用 Clear 进行 map 初始化操作。

## 使用字典

字典实现完成后，需要经过一个测试过程，查看这个字典是否存在问题。

将一些字符串和数值组合放入到字典中，然后再从字典中根据键查询出对应的值，接着再遍历一个字典中所有的元素。详细实现过程请参考下面的代码：

```
func main() {

    // 创建字典实例
    dict := NewDictionary()

    // 添加游戏数据
    dict.Set("My Factory", 60)
    dict.Set("Terra Craft", 36)
    dict.Set("Don't Hungry", 24)

    // 获取值及打印值
    favorite := dict.Get("Terra Craft")
    fmt.Println("favorite:", favorite)

    // 遍历所有的字典元素
    dict.Visit(func(key, value interface{}) bool {

        // 将值转为 int 类型, 并判断是否大于 40
        if value.(int) > 40 {

            // 输出“很贵”
            fmt.Println(key, "is expensive")
            return true
        }

        // 默认都是输出“很便宜”
        fmt.Println(key, "is cheap")

        return true
    })
}
```

代码说明如下：

*   第 4 行创建字典的实例。
*   第 7～9 行，将 3 组键值对通过字典的 Set() 方法设置到字典中。
*   第 12 行，根据字符串键查找值，将结果保存在 favorite 中。
*   第 13 行，打印 favorite 的值。
*   第 16 行，遍历字典的所有键值对。遍历的返回数据通过回调提供，key 是键，value 是值。
*   第 19 行，遍历返回的 key 和 value 的类型都是 interface{}，这里确认 value 只有 int 类型，所以将 value 转换为 int 类型判断是否大于 40。
*   第 23 和 29 行，继续遍历，返回 true
*   第 23 行，打印键。

运行代码，输出如下：
favorite: 36
My Factory is expensive
Terra Craft is cheap
Don't Hungry is cheap