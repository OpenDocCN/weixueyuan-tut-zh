# Python 设置进程启动的 2 种方式

根据平台的支持，Python 支持三种启动进程的方式：

#### 1) spawn

父进程会启动一个全新的 Python 解释器进程。在这种方式下，子进程只能继承那些处理 run() 方法所必需的资源。典型的，那些不必要的文件描述器和 handle 都不会被继承。使用这种方式来启动进程，其效率比使用 fork 或 forkserver 方式要低得多。

Windows 只支持 spawn 方式来启动进程，因此在 Windows 平台上默认使用这种方式来启动进程。

#### 2) fork

父进程使用 os.fork() 来启动一个 Python 解释器进程。在这种方式下，子进程会继承父进程的所有资源，因此子进程基本等效于父进程。这种方式只在 UNIX 平台上有效，UNIX 平台默认使用这种方式来启动进程。

#### 3) forkserver

如果使用这种方式来启动进程，程序将会启动一个服务器进程。在以后的时间内，当程序再次请求启动新进程时，父进程都会连接到该服务器进程，请求由服务器进程来 fork 新进程。通过这种方式启动的进程不需要从父进程继承资源。这种方式只在 UNIX 平台上有效。

从上面介绍可以看出，如果程序使用 UNIX 平台（包括 Linux 和 Mac OS X），Python 支持三种启动进程的方式；但如果使用 Windows 平台，则只能使用效率最低的 spawn 方式。

multiprocessing 模块提供了一个`set_start_method()` 函数，该函数可用于设置启动进程的方式（必须将这行设置代码放在所有与多进程有关的代码之前）。下面程序示范了显式设置启动进程的方式：

```
import multiprocessing
import os

def foo(q):
    print('被启动的新进程: (%s)' % os.getpid())
    q.put('Python')
if __name__ == '__main__':
    # 设置使用 fork 方式启动进程
    multiprocessing.set_start_method('spawn')
    q = multiprocessing.Queue()
    # 创建进程
    mp = multiprocessing.Process(target=foo, args=(q, ))
    # 启动进程
    mp.start()
    # 获取队列中的消息
    print(q.get())
    mp.join()
```

上面程序显式指定必须使用 fork 方式来启动进程，因此该程序只能在 UNIX 平台（包括 Linux、Mac OS X）上运行。上面代码实际上就相当于前面介绍的使用 os.fork() 方法来启动新进程。

在 Mac OS X 上运行上面程序，可以看到如下输出结果：

被启动的新进程：(1868)
Python

上面程序的新进程向 multiprocessing.Queue 中放入一个数据（Python 字符串），主进程取出该 Queue 中的数据，并输出该数据。

还有一种设置进程启动方式的方法，就是利用 get_context() 方法来获取 Context 对象，调用该方法时可传入 spawn、fork 或 forkserver 字符串。Context 拥有和 multiprocessing 相同的 API，因此程序可通过 Context 来创建并启动进程。例如如下程序：

```
import multiprocessing
import os

def foo(q):
    print('被启动的新进程: (%s)' % os.getpid())
    q.put('Python')
if __name__ == '__main__':
    # 设置使用 fork 方式启动进程，并获取 Context 对象
    ctx = multiprocessing.get_context('fork')
    # 接下来就可用 Context 对象来代替 mutliprocessing 模块了
    q = ctx.Queue()
    # 创建进程
    mp = ctx.Process(target=foo, args=(q, ))
    # 启动进程
    mp.start()
    # 获取队列中的消息
    print(q.get())
    mp.join()
```

上面程序中，第 9 行代码设置以 fork 方式启动进程，并获取 Context 对象，这样程序后面就可以使用 Context 对象来代替 multiprocessing 模块了。