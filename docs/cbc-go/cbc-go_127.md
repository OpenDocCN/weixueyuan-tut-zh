# goroutine（Go 语言并发）如何使用才更加高效？

Go 语言原生支持并发是被众人津津乐道的特性。goroutine 早期是 Inferno 操作系统的一个试验性特性，而现在这个特性与操作系统一起，将开发变得越来越简单。

很多刚开始使用 Go 语言开发的人都很喜欢使用并发特性，而没有考虑并发是否真正能解决他们的问题。

## 了解 goroutine 的生命期时再创建 goroutine

在 Go 语言中，开发者习惯将并发内容与 goroutine 一一对应地创建 goroutine。开发者很少会考虑 goroutine 在什么时候能退出和控制 goroutine 生命期，这就会造成 goroutine 失控的情况。下面来看一段代码。

失控的 goroutine：

```
package main

import (
    "fmt"
    "runtime"
)

// 一段耗时的计算函数
func consumer(ch chan int) {

    // 无限获取数据的循环
    for {

        // 从通道获取数据
        data := <-ch

        // 打印数据
        fmt.Println(data)
    }

}

func main() {

    // 创建一个传递数据用的通道
    ch := make(chan int)

    for {

        // 空变量, 什么也不做
        var dummy string

        // 获取输入, 模拟进程持续运行
        fmt.Scan(&dummy)

        // 启动并发执行 consumer()函数
        go consumer(ch)

        // 输出现在的 goroutine 数量
        fmt.Println("goroutines:", runtime.NumGoroutine())
    }

}
```

代码说明如下：

*   第 9 行，consumer() 函数模拟平时业务中放到 goroutine 中执行的耗时操作。该函数从其他 goroutine 中获取和接收数据或者指令，处理后返回结果。
*   第 12 行，需要通过无限循环不停地获取数据。
*   第 15 行，每次从通道中获取数据。
*   第 18 行，模拟处理完数据后的返回数据。
*   第 26 行，创建一个整型通道。
*   第 34 行，使用 fmt.Scan() 函数接收数据时，需要提供变量地址。如果输入匹配的变量类型，将会成功赋值给变量。
*   第 37 行，启动并发执行 consumer() 函数，并传入 ch 通道。
*   第 40 行，每启动一个 goroutine，使用 runtime.NumGoroutine 检查进程创建的 goroutine 数量总数。

运行程序，每输入一个字符串+回车，将会创建一个 goroutine，结果如下：

a
goroutines: 2
b
goroutines: 3
c
goroutines: 4

注意，结果中 a、b、c 为通过键盘输入的字符，其他为打印字符。

这个程序实际在模拟一个进程根据需要创建 goroutine 的情况。运行后，问题已经被暴露出来：随着输入的字符串越来越多，goroutine 将会无限制地被创建，但并不会结束。这种情况如果发生在生产环境中，将会造成内存大量分配，最终使进程崩溃。现实的情况也许比这段代码更加隐蔽：也许你设置了一个退出的条件，但是条件永远不会被满足或者触发。

为了避免这种情况，在这个例子中，需要为 consumer() 函数添加合理的退出条件，修改代码后如下：

```
package main

import (
    "fmt"
    "runtime"
)

// 一段耗时的计算函数
func consumer(ch chan int) {

    // 无限获取数据的循环
    for {

        // 从通道获取数据
        data := <-ch

        if data == 0 {
            break
        }

        // 打印数据
        fmt.Println(data)
    }

    fmt.Println("goroutine exit")
}

func main() {

    // 传递数据用的通道
    ch := make(chan int)

    for {

        // 空变量, 什么也不做
        var dummy string

        // 获取输入, 模拟进程持续运行
        fmt.Scan(&dummy)

        if dummy == "quit" {

            for i := 0; i < runtime.NumGoroutine()-1; i++ {
                ch <- 0
            }

            continue
        }

        // 启动并发执行 consumer()函数
        go consumer(ch)

        // 输出现在的 goroutine 数量
        fmt.Println("goroutines:", runtime.NumGoroutine())
    }
}
```

代码中加粗部分是新添加的代码，具体说明如下：

*   第 17 行，为无限循环设置退出条件，这里设置 0 为退出。
*   第 41 行，当命令行输入 quit 时，进入退出处理的流程。
*   第 43 行，runtime.NumGoroutine 返回一个进程的所有 goroutine 数，main() 的 goroutine 也被算在里面。因此需要扣除 main() 的 goroutine 数。剩下的 goroutine 为实际创建的 goroutine 数，对这些 goroutine 进行遍历。
*   第 44 行，并发开启的 goroutine 都在竞争获取通道中的数据，因此只要知道有多少个 goroutine 需要退出，就给通道里发多少个 0。

修改程序并运行，结果如下：

a
goroutines: 2
b
goroutines: 3
quit
goroutine exit
goroutine exit
c
goroutines: 2

## 避免在不必要的地方使用通道

通道（channel）和 map、切片一样，也是由 Go 源码编写而成。为了保证两个 goroutine 并发访问的安全性，通道也需要做一些锁操作，因此通道其实并不比锁高效。

下面的例子展示套接字的接收和并发管理。对于 TCP 来说，一般是接收过程创建 goroutine 并发处理。当套接字结束时，就要正常退出这些 goroutine。

本例完整代码请参考`./src/chapter12/exitnotify/exitnotify.go`。

> 本套教程所有源码下载地址：[`pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ`](https://pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ)    提取密码：hfyf

下面是对各个部分的详细分析。

#### 1) 套接字接收部分

套接字在连接后，就需要不停地接收数据，代码如下：

```
// 套接字接收过程
func socketRecv(conn net.Conn, exitChan chan string) {

// 创建一个接收的缓冲
    buff := make([]byte, 1024)

    // 不停地接收数据
    for {

        // 从套接字中读取数据
        _, err := conn.Read(buff)

        // 需要结束接收, 退出循环
        if err != nil {
            break
        }

    }

    // 函数已经结束, 发送通知
    exitChan <- "recv exit"
}
```

代码说明如下：

*   第 2 行传入的 net.Conn 是套接字的接口，exitChan 为退出发送同步通道。
*   第 5 行为套接字的接收数据创建一个缓冲。
*   第 8 行构建一个接收的循环，不停地接收数据。
*   第 11 行，从套接字中取出数据。这个例子中，不关注具体接收到的数据，只是关注错误，这里将接收到的字节数做匿名处理。
*   第 14 行，当套接字调用了 Close 方法时，会触发错误，这时需要结束接收循环。
*   第 21 行，结束函数时，与函数绑定的 goroutine 会同时结束，此时需要通知 main() 的 goroutine。

#### 2) 连接、关闭、同步 goroutine 主流程部分

下面代码中尝试使用套接字的 TCP 协议连接一个网址，连接上后，进行数据接收，等待一段时间后主动关闭套接字，等待套接字所在的 goroutine 自然结束，代码如下：

```
func main() {

    // 连接一个地址
    conn, err := net.Dial("tcp", "www.163.com:80")

    // 发生错误时打印错误退出
    if err != nil {
        fmt.Println(err)
        return
    }

    // 创建退出通道
    exit := make(chan string)

    // 并发执行套接字接收
    go socketRecv(conn, exit)

    // 在接收时, 等待 1 秒
    time.Sleep(time.Second)

    // 主动关闭套接字
    conn.Close()

    // 等待 goroutine 退出完毕
    fmt.Println(<-exit)
}
```

代码说明如下：

*   第 4 行，使用 net.Dial 发起 TCP 协议的连接，调用函数就会发送阻塞直到连接超时或者连接完成。
*   第 7 行，如果连接发生错误，将会打印错误并退出。
*   第 13 行，创建一个通道用于退出信号同步，这个通道会在接收用的 goroutine 中使用。
*   第 16 行，并发执行接收函数，传入套接字和用于退出通知的通道。
*   第 19 行，接收需要一个过程，使用 time.Sleep() 等待一段时间。
*   第 22 行，主动关闭套接字，此时会触发套接字接收错误。
*   第 25 行，从 exit 通道接收退出数据，也就是等待接收 goroutine 结束。

在这个例子中，goroutine 退出使用通道来通知，这种做法可以解决问题，但是实际上通道中的数据并没有完全使用。

#### 3) 优化：使用等待组替代通道简化同步

通道的内部实现代码在 Go 语言开发包的 src/runtime/chan.go 中，经过分析后大概了解到通道也是用常见的互斥量等进行同步。因此通道虽然是一个语言级特性，但也不是被神化的特性，通道的运行和使用都要比传统互斥量、等待组（sync.WaitGroup）有一定的消耗。

所以在这个例子中，更建议使用等待组来实现同步，调整后的代码如下：

```
package main

import (
    "fmt"
    "net"
    "sync"
    "time"
)

// 套接字接收过程
func socketRecv(conn net.Conn, wg *sync.WaitGroup) {

    // 创建一个接收的缓冲
    buff := make([]byte, 1024)

    // 不停地接收数据
    for {

        // 从套接字中读取数据
        _, err := conn.Read(buff)

        // 需要结束接收, 退出循环
        if err != nil {
            break
        }

    }

    // 函数已经结束, 发送通知
    wg.Done()
}

func main() {

    // 连接一个地址
    conn, err := net.Dial("tcp", "www.163.com:80")

    // 发生错误时打印错误退出
    if err != nil {
        fmt.Println(err)
        return
    }

    // 退出通道
    var wg sync.WaitGroup

    // 添加一个任务
    wg.Add(1)

    // 并发执行接收套接字
    go socketRecv(conn, &wg)

    // 在接收时, 等待 1 秒
    time.Sleep(time.Second)

    // 主动关闭套接字
    conn.Close()

    // 等待 goroutine 退出完毕
    wg.Wait()
    fmt.Println("recv done")
}
```

调整后的代码说明如下：

*   第 45 行，声明退出同步用的等待组。
*   第 48 行，为等待组的计数器加 1，表示需要完成一个任务。
*   第 51 行，将等待组的指针传入接收函数。
*   第 60 行，等待等待组的完成，完成后打印提示。
*   第 30 行，接收完成后，使用 wg.Done() 方法将等待组计数器减一。