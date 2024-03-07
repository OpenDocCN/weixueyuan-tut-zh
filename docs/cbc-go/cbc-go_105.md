# Go 语言竞态检测——检测代码在并发环境下可能出现的问题

Go 程序可以使用通道进行多个 goroutine 间的数据交换，但这仅仅是数据同步中的一种方法。通道内部的实现依然使用了各种锁，因此优雅代码的代价是性能。在某些轻量级的场合，原子访问（atomic 包）、互斥锁（sync.Mutex）以及等待组（sync.WaitGroup）能最大程度满足需求。

本节只讲解原子访问，互斥锁和等待组将在接下来的两节中讲解。

当多线程并发运行的程序竞争访问和修改同一块资源时，会发生竞态问题。

下面的代码中有一个 ID 生成器，每次调用生成器将会生成一个不会重复的顺序序号，使用 10 个并发生成序号，观察 10 个并发后的结果。

竞态检测的具体代码：

```
package main

import (
    "fmt"
    "sync/atomic"
)

var (
    // 序列号
    seq int64
)

// 序列号生成器
func GenID() int64 {

// 尝试原子的增加序列号
    atomic.AddInt64(&seq, 1)
    return seq
}

func main() {

    //生成 10 个并发序列号
    for i := 0; i < 10; i++ {
            go GenID()
    }

    fmt.Println(GenID())
}
```

代码说明如下：

*   第 10 行，序列号生成器中的保存上次序列号的变量。
*   第 17 行，使用原子操作函数 atomic.AddInt64() 对 seq() 函数加 1 操作。不过这里故意没有使用 atomic.AddInt64() 的返回值作为 GenID() 函数的返回值，因此会造成一个竞态问题。
*   第 25 行，循环 10 次生成 10 个 goroutine 调用 GenID() 函数，同时忽略 GenID() 的返回值。
*   第 28 行，单独调用一次 GenID() 函数。

在运行程序时，为运行参数加入`-race`参数，开启运行时（runtime）对竞态问题的分析，命令如下：

go run -race racedetect.go

代码运行发生宕机，输出信息如下：

==================
WARNING: DATA RACE
Write at 0x000000f52f40 by goroutine 7:
  sync/atomic.AddInt64()
      C:/Go/src/runtime/race_amd64.s:276 +0xb
  main.GenID()
      racedetect.go:17 +0x4a

Previous read at 0x000000f52f40 by goroutine 6:
  main.GenID()
      racedetect.go:18 +0x5a

Goroutine 7 (running) created at:
  main.main()
      racedetect.go:25 +0x5a

Goroutine 6 (finished) created at:
  main.main()
      racedetect.go:25 +0x5a
==================
10
Found 1 data race(s)
exit status 66

根据报错信息，第 18 行有竞态问题，根据 atomic.AddInt64() 的参数声明，这个函数会将修改后的值以返回值方式传出。下面代码对加粗部分进行了修改：

```
func GenID() int64 {
    // 尝试原子的增加序列号
    return atomic.AddInt64(&seq, 1)
}
```

再次运行：

go run -race main.go

代码输出如下：
10

没有发生竞态问题，程序运行正常。

本例中只是对变量进行增减操作，虽然可以使用互斥锁（sync.Mutex）解决竞态问题，但是对性能消耗较大。在这种情况下，推荐使用原子操作（atomic）进行变量操作。