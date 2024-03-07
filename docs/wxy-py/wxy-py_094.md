# Python thread 模块用法详解

> 原文：[`www.weixueyuan.net/a/667.html`](http://www.weixueyuan.net/a/667.html)

该包在 Python 2 中属于正常可用状态，但在 Python 3 中处于即将废弃的状态，虽然还可以用，但包名被改为 _thread。

使用 thread 包首先要引入该包，在 Python 2 中使用下面的语句来引入：

import thread

而在 Python 3 中，由于包名从 thread 改为了 _thread，所以需要使用下面的语句来引入：

import _thread

为了保持一致性，可以首先判断当前使用的是 Python 2 还是 Python 3，然后引入不同的包，并在 Python 3 中将包重命名为 thread。代码如下：

```

import sys
if sys.version_info.major == 2:   # Python 2
    import thread
else:                             # Python 3
    import _thread as thread
```

## 线程的启动

在 thread 包中，需要定义一个函数作为线程的入口。我们可以编写下面的一个函数，其每隔一秒钟就打印一句话，打印 10 次后退出。代码如下：

```

def thread_entry(id):
    cnt = 0
    while cnt < 10:          # 循环次数不够
print('Thread:(%d) Time:%s' % (id, time.ctime()))
time.sleep(1)               # 休息一秒
cnt = cnt + 1
```

另外，如果需要启动某个线程，可以使用下面的方法:

start_new_thread(function, args[, kwargs])

该方法的第一个参数 function 表示要执行的函数，如上面定义的函数名，该函数将作为线程的入口函数使用。args 和 kwargs 是该函数的参数，args 是必须的，类型是元组；kwargs 是可选的，类型是字典。

这里仅使用 args，而且只有一个参数，代码如下：

```

def start_threads():
    t1 = thread.start_new_thread(thread_entry, (1,))    # 启动线程 1
    t2 = thread.start_new_thread(thread_entry, (2,))    # 启动线程 2
    time.sleep(12)      # 等待
```

函数 start_threads() 将会启动两个线程，每个线程都是以 thread_entry() 作为入口函数。下面是完整的代码。

```

import time, sys                      # 引入 time 和 sys 模块
if sys.version_info.major == 2:       # 如果当前使用的是 Python 2 解释器
     import thread
else:                                 # 当前使用的是 Python 3 解释器
     import _thread as thread
def thread_entry(id):                 # 定义入口函数
cnt = 0
while cnt < 10:                      #循环 10 次，每次打印一行
        print('Thread:(%d) Time:%s' % (id, time.ctime()))
        time.sleep(1)
        cnt = cnt + 1
def start_threads():                  # 启动线程
    t1 = thread.start_new_thread(thread_entry, (1,))
    t2 = thread.start_new_thread(thread_entry, (2,))
    time.sleep(12)
if __name__=='__main__':              # 运行脚本
    start_threads()
```

运行该脚本，输出如下：

$ python3 createThreadDemo1.py
Thread:(1) Time:Fri May 10 06:00:29 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:29 2019                # 线程 2 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:30 2019                # 线程 2 第 9 行输出
Thread:(1) Time:Fri May 10 06:00:30 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:31 2019
Thread:(1) Time:Fri May 10 06:00:31 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:32 2019                # 线程 2 第 9 行输出
Thread:(1) Time:Fri May 10 06:00:32 2019
Thread:(2) Time:Fri May 10 06:00:33 2019                # 线程 2 第 9 行输出
Thread:(1) Time:Fri May 10 06:00:33 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:34 2019
Thread:(1) Time:Fri May 10 06:00:34 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:35 2019                # 线程 2 第 9 行输出
Thread:(1) Time:Fri May 10 06:00:35 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:36 2019
Thread:(1) Time:Fri May 10 06:00:36 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:37 2019                # 线程 2 第 9 行输出
Thread:(1) Time:Fri May 10 06:00:37 2019                # 线程 1 第 9 行输出
Thread:(2) Time:Fri May 10 06:00:38 2019
Thread:(1) Time:Fri May 10 06:00:38 2019

可以看到 Tread1 和 Tread2 交错运行，并不是某一个线程执行完之后另外一个再执行。这两个线程的执行顺序并没有特定的规律，具有一定的随机性，所以两个线程可以看作是并行的。

## 线程的退出

上面介绍了如何定义入口函数和启动线程，那么线程如何退出呢？有下面几种退出方式：

*   入口函数执行完毕。
*   线程抛出没有处理的异常。
*   线程内部调用 _thread.exit_thread()来退出。

前面的例子是通过入口函数执行完毕来退出线程的，下面的例子演示了使用 _thread.exit_thread() 来退出线程的方法。在这个例子中，入口函数通过一个全局变量来判断是否需要退出。而这个全局变量是被主线程来控制的。完整代码如下：

```

import time, sys
if sys.version_info.major == 2:      # Python 2
    import thread
else:                                # Python 3
    import _thread as thread
g_continue = True                    # 继续执行的标志
def thread_entry(id):                # 线程入口函数
    global g_continue
    while True:
        if not g_continue:           # 如果标志为 False，退出
            print("Thread:(%d) exit" % id)
            thread.exit_thread()     # 退出线程
        else:
            print('Thread:(%d) Time:%s' % (id, time.ctime()))
            time.sleep(1)
def start_threads():                 # 启动线程
    global g_continue
    t1 = thread.start_new_thread(thread_entry, (1,))    # 启动线程 1
    t2 = thread.start_new_thread(thread_entry, (2,))    # 启动线程 2
    time.sleep(3)
    g_continue = False                                  # 修改标志
    time.sleep(2)
if __name__=='__main__':
    start_threads()
```

运行结果如下：

$ python3 createThreadDemo2.py              # 运行
Thread:(1) Time:Fri May 10 06:16:35 2019    # 线程 1 在运行
Thread:(2) Time:Fri May 10 06:16:35 2019    # 线程 2 在运行
Thread:(1) Time:Fri May 10 06:16:36 2019
Thread:(2) Time:Fri May 10 06:16:36 2019
Thread:(2) Time:Fri May 10 06:16:37 2019
Thread:(1) Time:Fri May 10 06:16:37 2019
Thread:(1) exit                             # 线程 1 退出
Thread:(2) exit                             # 线程 2 退出