# Python schedule 任务调度及其用法

如果需要执行更复杂的任务调度，则可使用 Python 提供的 sched 模块。该模块提供了 sched.scheduler 类，该类代表一个任务调度器。

`sched.scheduler(timefunc=time.monotonic, delayfunc=time.sleep)` 构造器支持两个参数：

1.  timefunc：该参数指定生成时间戳的时间函数，默认使用 time.monotonic 来生成时间戳。
2.  delayfunc：该参数指定阻塞程序的函数，默认使用 time.sleep 函数来阻塞程序。

sched.scheduler 调度器支持如下常用属性和方法：

*   scheduler.enterabs(time, priority, action, argument=(), kwargs={})：指定在 time 时间点执行 action 函数，argument 和 kwargs 都用于向 action 函数传入参数，其中 argument 使用位置参数的形式传入参数，kwargs 使用关键字参数的形式传入参数。该方法返回一个 event，它可作为 cancel() 方法的参数用于取消该调度。priority 参数指定该任务的优先级，当在同一个时间点有多个任务需要执行时，优先级高（值越小代表优先级越高）的任务会优先执行。
*   scheduler.enter(delay, priority, action, argument=(),kwargs={})：该方法与上一个方法基本相同，只是 delay 参数用于指定多少秒之后执行 action 任务。
*   scheduler.cancel(event)：取消任务。如果传入的 event 参数不是当前调度队列中的 event，程序将会引发 ValueError 异常。
*   scheduler.empty()：判断当前该调度器的调度队列是否为空。
*   scheduler.run(blocking=True)：运行所有需要调度的任务。如果调用该方法的 blocking 参数为 True，该方法将会阻塞线程，直到所有被调度的任务都执行完成。
*   scheduler.queue：该只读属性返回该调度器的调度队列。

下面程序示范了使用 sched.scheduler 来执行任务调度：

```
import sched, time
import threading

# 定义线程调度器
s = sched.scheduler()

# 定义被调度的函数
def print_time(name='default'):
    print("%s 的时间: %s" % (name, time.ctime()))
print('主线程：', time.ctime())
# 指定 10 秒之后执行 print_time 函数
s.enter(10, 1, print_time)
# 指定 5 秒之后执行 print_time 函数，优先级为 2
s.enter(5, 2, print_time, argument=('位置参数',))
# 指定 5 秒之后执行 print_time 函数，优先级为 1
s.enter(5, 1, print_time, kwargs={'name': '关键字参数'})
# 执行调度的任务
s.run()
print('主线程：', time.ctime())
```

上面程序中，第 12 行代码指定 10s 后执行 print_time() 函数，本次调度没有为该函数传入参数；第 14 行代码指定 5s 后调度 print_time() 函数，本次调度使用位置参数的形式为该函数传入参数；第 16 行代码指定 5s 后调度 print_time() 函数，本次调度使用关键字参数的形式为该函数传入参数。

上面程序运行后，将会看到程序在 5s 后执行两次 print_time() 函数，其中传入关键字参数的函数先执行（它的优先级更高），10s 后执行一次 print_time() 函数。运行上面程序，将看到如下输出结果：

主线程： Tue Mar  5 16:30:51 2019
关键字参数 的时间: Tue Mar  5 16:30:56 2019
位置参数 的时间: Tue Mar  5 16:30:56 2019
default 的时间: Tue Mar  5 16:31:01 2019
主线程： Tue Mar  5 16:31:01 2019