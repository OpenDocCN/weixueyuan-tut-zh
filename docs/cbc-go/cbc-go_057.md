# Go 语言 defer（延迟执行语句）

Go 语言的 defer 语句会将其后面跟随的语句进行延迟处理。在 defer 归属的函数即将返回时，将延迟处理的语句按 defer 的逆序进行执行，也就是说，先被 defer 的语句最后被执行，最后被 defer 的语句，最先被执行。

## 多个延迟执行语句的处理顺序

下面的代码是将一系列的数值打印语句按顺序延迟处理，参见演示代码：

```
package main

import (
    "fmt"
)

func main() {

    fmt.Println("defer begin")

    // 将 defer 放入延迟调用栈
    defer fmt.Println(1)

    defer fmt.Println(2)

    // 最后一个放入, 位于栈顶, 最先调用
    defer fmt.Println(3)

    fmt.Println("defer end")
}
```

代码输出如下：
defer begin
defer end
3
2
1

结果分析如下：

*   代码的延迟顺序与最终的执行顺序是反向的。
*   延迟调用是在 defer 所在函数结束时进行，函数结束可以是正常返回时，也可以是发生宕机时。

## 使用延迟执行语句在函数退出时释放资源

处理业务或逻辑中涉及成对的操作是一件比较烦琐的事情，比如打开和关闭文件、接收请求和回复请求、加锁和解锁等。在这些操作中，最容易忽略的就是在每个函数退出处正确地释放和关闭资源。

defer 语句正好是在函数退出时执行的语句，所以使用 defer 能非常方便地处理资源释放问题。

#### 1) 使用延迟并发解锁

在下面的例子中会在函数中并发使用 map，为防止竞态问题，使用 sync.Mutex 进行加锁，参见下面代码：

```
var (
    // 一个演示用的映射
    valueByKey      = make(map[string]int)
    // 保证使用映射时的并发安全的互斥锁
    valueByKeyGuard sync.Mutex
)

// 根据键读取值
func readValue(key string) int {
    // 对共享资源加锁
    valueByKeyGuard.Lock()
    // 取值
    v := valueByKey[key]
    // 对共享资源解锁
    valueByKeyGuard.Unlock()
    // 返回值
    return v
}
```

代码说明如下：

*   第 3 行，实例化一个 map，键是 string 类型，值为 int。
*   第 5 行，map 默认不是并发安全的，准备一个 sync.Mutex 互斥量保护 map 的访问。
*   第 9 行，readValue() 函数给定一个键，从 map 中获得值后返回，该函数会在并发环境中使用，需要保证并发安全。
*   第 11 行，使用互斥量加锁。
*   第 13 行，从 map 中获取值。
*   第 15 行，使用互斥量解锁。
*   第 17 行，返回获取到的 map 值。

使用 defer 语句对上面的语句进行简化，参考下面的代码。

```
func readValue(key string) int {

    valueByKeyGuard.Lock()

    // defer 后面的语句不会马上调用, 而是延迟到函数结束时调用
    defer valueByKeyGuard.Unlock()

    return valueByKey[key]
}
```

加粗部分为对比前面代码而修改和添加的代码，代码说明如下：

*   第 6 行在互斥量加锁后，使用 defer 语句添加解锁，该语句不会马上执行，而是等 readValue() 返回时才会被执行。
*   第 8 行，从 map 查询值并返回的过程中，与不使用互斥量的写法一样，对比上面的代码，这种写法更简单。

#### 2) 使用延迟释放文件句柄

文件的操作需要经过打开文件、获取和操作文件资源、关闭资源几个过程，如果在操作完毕后不关闭文件资源，进程将一直无法释放文件资源。在下面的例子中将实现根据文件名获取文件大小的函数，函数中需要打开文件、获取文件大小和关闭文件等操作。由于每一步系统操作都需要进行错误处理，而每一步处理都会造成一次可能的退出，因此就需要在退出时释放资源，而我们需要密切关注在函数退出处正确地释放文件资源。参考下面的代码：

```
// 根据文件名查询其大小
func fileSize(filename string) int64 {

    // 根据文件名打开文件, 返回文件句柄和错误
    f, err := os.Open(filename)

    // 如果打开时发生错误, 返回文件大小为 0
    if err != nil {
        return 0
    }

    // 取文件状态信息
    info, err := f.Stat()

    // 如果获取信息时发生错误, 关闭文件并返回文件大小为 0
    if err != nil {
        f.Close()
        return 0
    }

    // 取文件大小
    size := info.Size()

    // 关闭文件
    f.Close()

    // 返回文件大小
    return size
}
```

代码说明如下：

*   第 2 行，定义获取文件大小的函数，返回值是 64 位的文件大小值。
*   第 5 行，使用 os 包提供的函数 Open()，根据给定的文件名打开一个文件，并返回操作文件用的句柄和操作错误。
*   第 8 行，如果打开的过程中发生错误，如文件没找到、文件被占用等，将返回文件大小为 0。
*   第 13 行，此时文件句柄 f 可以正常使用，使用 f 的方法 Stat() 来获取文件的信息，获取信息时，可能也会发生错误。
*   第 16～19 行对错误进行处理，此时文件是正常打开的，为了释放资源，必须要调用 f 的 Close() 方法来关闭文件，否则会发生资源泄露。
*   第 22 行，获取文件大小。
*   第 25 行，关闭文件、释放资源。
*   第 28 行，返回获取到的文件大小。

在上面的例子中加粗部分是对文件的关闭操作。下面使用 defer 对代码进行简化，代码如下：

```
func fileSize(filename string) int64 {

    f, err := os.Open(filename)

    if err != nil {
        return 0
    }

    // 延迟调用 Close, 此时 Close 不会被调用
    defer f.Close()

    info, err := f.Stat()

    if err != nil {
        // defer 机制触发, 调用 Close 关闭文件
        return 0
    }

    size := info.Size()

    // defer 机制触发, 调用 Close 关闭文件
    return size
}
```

代码中加粗部分为对比前面代码而修改的部分，代码说明如下：

*   第 10 行，在文件正常打开后，使用 defer，将 f.Close() 延迟调用。注意，不能将这一句代码放在第 4 行空行处，一旦文件打开错误，f 将为空，在延迟语句触发时，将触发宕机错误。
*   第 16 行和第 22 行，defer 后的语句（f.Close()）将会在函数返回前被调用，自动释放资源。