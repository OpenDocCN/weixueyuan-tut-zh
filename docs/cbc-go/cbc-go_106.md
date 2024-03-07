# Go 语言互斥锁（sync.Mutex）和读写互斥锁（sync.RWMutex）

互斥锁是一种常用的控制共享资源访问的方法，它能够保证同时只有一个 goroutine 可以访问共享资源。在 Go 程序中的使用非常简单，参见下面的代码：

```
package main

import (
    "fmt"
    "sync"
)

var (
    // 逻辑中使用的某个变量
    count int

    // 与变量对应的使用互斥锁
    countGuard sync.Mutex
)

func GetCount() int {

    // 锁定
    countGuard.Lock()

    // 在函数退出时解除锁定
    defer countGuard.Unlock()

    return count
}

func SetCount(c int) {
    countGuard.Lock()
    count = c
    countGuard.Unlock()
}

func main() {

    // 可以进行并发安全的设置
    SetCount(1)

    // 可以进行并发安全的获取
    fmt.Println(GetCount())

}
```

代码说明如下：

*   第 10 行是某个逻辑步骤中使用到的变量，无论是包级的变量还是结构体成员字段，都可以。
*   第 13 行，一般情况下，建议将互斥锁的粒度设置得越小越好，降低因为共享访问时等待的时间。这里笔者习惯性地将互斥锁的变量命名为以下格式：

    变量名+Guard

    以表示这个互斥锁用于保护这个变量。
*   第 16 行是一个获取 count 值的函数封装，通过这个函数可以并发安全的访问变量 count。
*   第 19 行，尝试对 countGuard 互斥量进行加锁。一旦 countGuard 发生加锁，如果另外一个 goroutine 尝试继续加锁时将会发生阻塞，直到这个 countGuard 被解锁。
*   第 22 行使用 defer 将 countGuard 的解锁进行延迟调用，解锁操作将会发生在 GetCount() 函数返回时。
*   第 27 行在设置 count 值时，同样使用 countGuard 进行加锁、解锁操作，保证修改 count 值的过程是一个原子过程，不会发生并发访问冲突。

在读多写少的环境中，可以优先使用读写互斥锁（sync.RWMutex），它比互斥锁更加高效。sync 包中的 RWMutex 提供了读写互斥锁的封装。

我们将互斥锁例子中的一部分代码修改为读写互斥锁，参见下面代码：

```
var (
    // 逻辑中使用的某个变量
    count int

    // 与变量对应的使用互斥锁
    countGuard sync.RWMutex
)

func GetCount() int {

    // 锁定
    countGuard.RLock()

    // 在函数退出时解除锁定
    defer countGuard.RUnlock()

    return count
}
```

代码说明如下：

*   第 6 行，在声明 countGuard 时，从 sync.Mutex 互斥锁改为 sync.RWMutex 读写互斥锁。
*   第 12 行，获取 count 的过程是一个读取 count 数据的过程，适用于读写互斥锁。在这一行，把 countGuard.Lock() 换做 countGuard.RLock()，将读写互斥锁标记为读状态。如果此时另外一个 goroutine 并发访问了 countGuard，同时也调用了 countGuard.RLock() 时，并不会发生阻塞。
*   第 15 行，与读模式加锁对应的，使用读模式解锁。