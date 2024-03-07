# Python Process 创建进程（2 种方法）详解

虽然使用 os.fork() 方法可以启动多个进程，但这种方式显然不适合 Windows，而 Python 是跨平台的语言，所以 Python 绝不能仅仅局限于 Windows 系统，因此 Python 也提供了其他方式在 Windows 下创建新进程。

Python 在 multiprocessing 模块下提供了 Process 来创建新进程。与 Thread 类似的是，使用 Process 创建新进程也有两种方式：

1.  以指定函数作为 target，创建 Process 对象即可创建新进程。
2.  继承 Process 类，并重写它的 run() 方法来创建进程类，程序创建 Process 子类的实例作为进程。

Process 类也有如下类似的方法和属性：

*   run()：重写该方法可实现进程的执行体。
*   start()：该方法用于启动进程。
*   join([timeout])：该方法类似于线程的 join() 方法，当前进程必须等待被 join 的进程执行完成才能向下执行。
*   name：该属性用于设置或访问进程的名字。
*   is_alive()：判断进程是否还活着。
*   daemon：该属性用于判断或设置进程的后台状态。
*   pid：返回进程的 ID。
*   authkey：返回进程的授权 key。
*   terminate()：中断该进程。

## 以指定函数作为 target 创建新进程

下面先介绍以指定函数作为 target 来创建新进程。

```
import multiprocessing
import os

# 定义一个普通的 action 函数，该函数准备作为进程执行体
def action(max):
    for i in range(max):
        print("(%s)子进程（父进程:(%s)）：%d" %
            (os.getpid(), os.getppid(), i))
if __name__ == '__main__':
    # 下面是主程序（也就是主进程）
    for i in range(100):
        print("(%s)主进程: %d" % (os.getpid(), i))
        if i == 20:
            # 创建并启动第一个进程
            mp1 = multiprocessing.Process(target=action,args=(100,))
            mp1.start()
            # 创建并启动第一个进程
            mp2 = multiprocessing.Process(target=action,args=(100,))
            mp2.start()
            mp2.join()
    print('主进程执行完成!')
```

上面程序中第 15、16 两行代码就是程序创建并启动新进程的关键代码，不难发现这两行代码和创建并启动新线程的代码几乎一样，只是此处创建的是 multiprocessing.Process 对象。

需要说明的是，通过 multiprocessing.Process 来创建并启动进程时，程序必须先判断`if __name__=='__main__':`，否则可能引发异常。

运行上面程序，可以看到程序中运行了三个进程，一个主进程和程序启动的两个子进程。由于程序中调用了 mp2.join()，因此主进程必须等 mp2 进程完成后才能向下执行。

## 继承 Process 类创建子进程

继承 Process 类创建子进程的步骤如下：

1.  定义继承 Process 的子类，重写其 run() 方法准备作为进程执行体。
2.  创建 Process 子类的实例。
3.  调用 Process 子类的实例的 start() 方法来启动进程。

下面程序通过继承 Process 类来创建子进程：

```
import multiprocessing
import os

class MyProcess(multiprocessing.Process):
    def __init__(self, max):
        self.max = max
        super().__init__()
    # 重写 run()方法作为进程执行体
    def run(self):
        for i in range(self.max):
            print("(%s)子进程（父进程:(%s)）：%d" %
                (os.getpid(), os.getppid(), i))
if __name__ == '__main__':
    # 下面是主程序（也就是主进程）
    for i in range(100):
        print("(%s)主进程: %d" % (os.getpid(), i))
        if i == 20:
            # 创建并启动第一个进程
            mp1 = MyProcess(100)
            mp1.start()
            # 创建并启动第一个进程
            mp2 = MyProcess(100)
            mp2.start()
            mp2.join()
    print('主进程执行完成!')
```

该程序的运行结果与上一个程序的运行结果大致相同，它们只是创建进程的方式略有不同而已。

通常，推荐使用第一种方式来创建进程，因为这种方式不仅编程简单，而且进程直接包装 target 函数，具有更清晰的逻辑结构。