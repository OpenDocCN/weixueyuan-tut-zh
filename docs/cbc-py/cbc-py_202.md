# Python 使用进程池管理进程

与线程池类似的是，如果程序需要启动多个进程，也可以使用进程池来管理进程。程序可以通过 multiprocessing 模块的 Pool() 函数创建进程池，进程池实际上是 multiprocessing.pool.Pool 类。

进程池具有如下常用方法：

*   apply(func[, args[, kwds]])：将 func 函数提交给进程池处理。其中 args 代表传给 func 的位置参数，kwds 代表传给 func 的关键字参数。该方法会被阻塞直到 func 函数执行完成。
*   apply_async(func[, args[, kwds[, callback[, error_callback]]]])：这是 apply() 方法的异步版本，该方法不会被阻塞。其中 callback 指定 func 函数完成后的回调函数，error_callback 指定 func 函数出错后的回调函数。
*   map(func, iterable[, chunksize])：类似于 Python 的 map() 全局函数，只不过此处使用新进程对 iterable 的每一个元素执行 func 函数。
*   map_async(func, iterable[, chunksize[, callback[, error_callback]]])：这是 map() 方法的异步版本，该方法不会被阻塞。其中 callback 指定 func 函数完成后的回调函数，error_callback 指定 func 函数出错后的回调函数。
*   imap(func, iterable[, chunksize])：这是 map() 方法的延迟版本。
*   imap_unordered(func, iterable[, chunksize])：功能类似于 imap() 方法，但该方法不能保证所生成的结果（包含多个元素）与原 iterable 中的元素顺序一致。
*   starmap(func, iterable[,chunksize])：功能类似于 map() 方法，但该方法要求 iterable 的元素也是 iterable 对象，程序会将每一个元素解包之后作为 func 函数的参数。
*   close()：关闭进程池。在调用该方法之后，该进程池不能再接收新任务，它会把当前进程池中的所有任务执行完成后再关闭自己。
*   terminate()：立即中止进程池。
*   join()：等待所有进程完成。

从上面介绍不难看出，如果程序只是想将任务提交给进程池执行，则可调用 apply() 或 apply_async() 方法；如果程序需要使用指定函数将 iterable 转换成其他 iterable，则可使用 map() 或 imap() 方法。

下面程序示范了使用 apply_async() 方法启动进程：

```
import multiprocessing
import time
import os

def action(name='default'):
    print('(%s)进程正在执行，参数为: %s' % (os.getpid(), name))
    time.sleep(3)
if __name__ == '__main__':
    # 创建包含 4 条进程的进程池
    pool = multiprocessing.Pool(processes=4)
    # 将 action 分 3 次提交给进程池
    pool.apply_async(action)
    pool.apply_async(action, args=('位置参数', ))
    pool.apply_async(action, kwds={'name': '关键字参数'})
    pool.close()
    pool.join()
```

上面程序创建了一个进程池，其中第 12-14 行代码都负责将 action 提交给进程池，只是每次提交时指定参数的方式不同。

运行上面程序，可以看到如下输出结果：

(14304)进程正在执行，参数为：default
(9344)进程正在执行，参数为：位置参数
(13796)进程正在执行，参数为：关键字参数

从上面的输出结果可以看到，程序分别使用 3 个进程来执行 action 任务。

从上面程序可以看出，进程池同样实现了上下文管理协议，因此程序可以使用 with 子句来管理进程池，这样就可以避免程序主动关闭进程池。

下面程序示范了使用 map() 方法来启动进程：

```
import multiprocessing
import time
import os

# 定义一个准备作为进程任务的函数
def action(max):
    my_sum = 0
    for i in range(max):
        print('(%s)进程正在执行: %d' % (os.getpid(), i))
        my_sum += i
    return my_sum
if __name__ == '__main__':
    # 创建一个包含 4 条进程的进程池
    with multiprocessing.Pool(processes=4) as pool:
        # 使用进程执行 map 计算
        # 后面元组有 3 个元素，因此程序启动 3 条进程来执行 action 函数
        results = pool.map(action, (50, 100, 150))
        print('--------------')
        for r in results:
            print(r)
```

运行上面程序，可以看到程序启动 3 个进程来执行 action 函数，程序最后输出 0~50、0~100、0~150 的累加结果。

可能读者已经发现了，其实该程序与前面介绍线程池的 map() 方法时所用的示例程序几乎一样。事实就是如此，只不过前面程序使用了更轻量级的线程来实现并发，而此处则使用进程来实现并发。这两种方式殊途同归，但相比之下，使用线程会有更好的性能，因此一般推荐使用多线程来实现并发。