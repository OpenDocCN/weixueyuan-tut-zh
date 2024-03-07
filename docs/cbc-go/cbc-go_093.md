# Go 语言 GOMAXPROCS（调整并发的运行性能）

在 Go 程序运行时（runtime）实现了一个小型的任务调度器。这套调度器的工作原理类似于操作系统调度线程，Go 程序调度器可以高效地将 CPU 资源分配给每一个任务。传统逻辑中，开发者需要维护线程池中线程与 CPU 核心数量的对应关系。同样的，Go 地中也可以通过 runtime.GOMAXPROCS() 函数做到，格式为：

runtime.GOMAXPROCS(逻辑 CPU 数量)

这里的逻辑 CPU 数量可以有如下几种数值：

*   <1：不修改任何数值。
*   =1：单核心执行。
*   >1：多核并发执行。

一般情况下，可以使用 runtime.NumCPU() 查询 CPU 数量，并使用 runtime.GOMAXPROCS() 函数进行设置，例如：

```
runtime.GOMAXPROCS(runtime.NumCPU())
```

Go 1.5 版本之前，默认使用的是单核心执行。从 Go 1.5 版本开始，默认执行上面语句以便让代码并发执行，最大效率地利用 CPU。

GOMAXPROCS 同时也是一个环境变量，在应用程序启动前设置环境变量也可以起到相同的作用。