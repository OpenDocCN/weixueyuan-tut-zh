# C# Thread：线程及与线程有关的类

> 原文：[`c.biancheng.net/view/2993.html`](http://c.biancheng.net/view/2993.html)

在 C# 语言中线程（Thread）是包含在进程中的，它位于 System.Threading 命名空间中。

与线程有关的类同样也都在 System.Threading 命名空间中，主要的类如下表所示。

| 类名 | 说明 |
| Thread | 在初始的应用程序中创建其他的线程 |
| ThreadState | 指定 Thread 的执行状态，包括开始、运行、挂起等 |
| ThreadPriority | 线程在调度时的优先级枚举值，包括 Highest、AboveNormal、Normal、BelowNormal、Lowest |
| ThreadPool | 提供一个线程池，用于执行任务、发送工作项、处理异步 I/O 等操作 |
| Monitor | 提供同步访问对象的机制 |
| Mutex | 用于线程间同步的操作 |
| ThreadAbortException | 调用 Thread 类中的 Abort 方法时出现的异常 |
| ThreadStateException | Thead 处于对方法调用无效的 ThreadState 时出现的异常 |

Thread 类主要用于实现线程的创建以及执行，其常用的属性和方法如下表所示。

| 属性或方法 | 说明 |
| Name | 属性，获取或设置线程的名称 |
| Priority | 属性，获取或设置线程的优先级 |
| ThreadState | 属性，获取线程当前的状态 |
| IsAlive  | 属性，获取当前线程是否处于启动状态 |
| IsBackground | 属性，获取或设置值，表示该线程是否为后台线程 |
| CurrentThread | 属性，获取当前正在运行的线程 |
| Start()  | 方法，启动线程 |
| Sleep(int millisecondsTimout) | 方法，将当前线程暂停指定的毫秒数 |
| Suspend() | 方法，挂起当前线程（已经被弃用） |
| Join() | 方法，阻塞调用线程，直到某个线程终止为止 |
| Interrupt() | 方法，中断当前线程 |
| Resume() | 方法，继续已经挂起的线程（已经被弃用） |
| Abort() | 方法，终止线程 |