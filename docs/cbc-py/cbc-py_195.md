# Python 线程池及其原理和使用（超级详细）

系统启动一个新线程的成本是比较高的，因为它涉及与操作系统的交互。在这种情形下，使用线程池可以很好地提升性能，尤其是当程序中需要创建大量生存期很短暂的线程时，更应该考虑使用线程池。

线程池在系统启动时即创建大量空闲的线程，程序只要将一个函数提交给线程池，线程池就会启动一个空闲的线程来执行它。当该函数执行结束后，该线程并不会死亡，而是再次返回到线程池中变成空闲状态，等待执行下一个函数。

此外，使用线程池可以有效地控制系统中并发线程的数量。当系统中包含有大量的并发线程时，会导致系统性能急剧下降，甚至导致 Python 解释器崩溃，而线程池的最大线程数参数可以控制系统中并发线程的数量不超过此数。

## 线程池的使用

线程池的基类是 concurrent.futures 模块中的 Executor，Executor 提供了两个子类，即 ThreadPoolExecutor 和 ProcessPoolExecutor，其中 ThreadPoolExecutor 用于创建线程池，而 ProcessPoolExecutor 用于创建进程池。

如果使用线程池/进程池来管理并发编程，那么只要将相应的 task 函数提交给线程池/进程池，剩下的事情就由线程池/进程池来搞定。

Exectuor 提供了如下常用方法：

*   submit(fn, *args, **kwargs)：将 fn 函数提交给线程池。*args 代表传给 fn 函数的参数，*kwargs 代表以关键字参数的形式为 fn 函数传入参数。
*   map(func, *iterables, timeout=None, chunksize=1)：该函数类似于全局函数 map(func, *iterables)，只是该函数将会启动多个线程，以异步方式立即对 iterables 执行 map 处理。
*   shutdown(wait=True)：关闭线程池。

程序将 task 函数提交（submit）给线程池后，submit 方法会返回一个 Future 对象，Future 类主要用于获取线程任务函数的返回值。由于线程任务会在新线程中以异步方式执行，因此，线程执行的函数相当于一个“将来完成”的任务，所以 Python 使用 Future 来代表。

实际上，在 Java 的多线程编程中同样有 Future，此处的 Future 与 Java 的 Future 大同小异。

Future 提供了如下方法：

*   cancel()：取消该 Future 代表的线程任务。如果该任务正在执行，不可取消，则该方法返回 False；否则，程序会取消该任务，并返回 True。
*   cancelled()：返回 Future 代表的线程任务是否被成功取消。
*   running()：如果该 Future 代表的线程任务正在执行、不可被取消，该方法返回 True。
*   done()：如果该 Funture 代表的线程任务被成功取消或执行完成，则该方法返回 True。
*   result(timeout=None)：获取该 Future 代表的线程任务最后返回的结果。如果 Future 代表的线程任务还未完成，该方法将会阻塞当前线程，其中 timeout 参数指定最多阻塞多少秒。
*   exception(timeout=None)：获取该 Future 代表的线程任务所引发的异常。如果该任务成功完成，没有异常，则该方法返回 None。
*   add_done_callback(fn)：为该 Future 代表的线程任务注册一个“回调函数”，当该任务成功完成时，程序会自动触发该 fn 函数。

在用完一个线程池后，应该调用该线程池的 shutdown() 方法，该方法将启动线程池的关闭序列。调用 shutdown() 方法后的线程池不再接收新任务，但会将以前所有的已提交任务执行完成。当线程池中的所有任务都执行完成后，该线程池中的所有线程都会死亡。

使用线程池来执行线程任务的步骤如下：

1.  调用 ThreadPoolExecutor 类的构造器创建一个线程池。
2.  定义一个普通函数作为线程任务。
3.  调用 ThreadPoolExecutor 对象的 submit() 方法来提交线程任务。
4.  当不想提交任何任务时，调用 ThreadPoolExecutor 对象的 shutdown() 方法来关闭线程池。

下面程序示范了如何使用线程池来执行线程任务：

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time

# 定义一个准备作为线程任务的函数
def action(max):
    my_sum = 0
    for i in range(max):
        print(threading.current_thread().name + '  ' + str(i))
        my_sum += i
    return my_sum
# 创建一个包含 2 条线程的线程池
pool = ThreadPoolExecutor(max_workers=2)
# 向线程池提交一个 task, 50 会作为 action()函数的参数
future1 = pool.submit(action, 50)
# 向线程池再提交一个 task, 100 会作为 action()函数的参数
future2 = pool.submit(action, 100)
# 判断 future1 代表的任务是否结束
print(future1.done())
time.sleep(3)
# 判断 future2 代表的任务是否结束
print(future2.done())
# 查看 future1 代表的任务返回的结果
print(future1.result())
# 查看 future2 代表的任务返回的结果
print(future2.result())
# 关闭线程池
pool.shutdown()
```

上面程序中，第 13 行代码创建了一个包含两个线程的线程池，接下来的两行代码只要将 action() 函数提交（submit）给线程池，该线程池就会负责启动线程来执行 action() 函数。这种启动线程的方法既优雅，又具有更高的效率。

当程序把 action() 函数提交给线程池时，submit() 方法会返回该任务所对应的 Future 对象，程序立即判断 futurel 的 done() 方法，该方法将会返回 False（表明此时该任务还未完成）。接下来主程序暂停 3 秒，然后判断 future2 的 done() 方法，如果此时该任务已经完成，那么该方法将会返回 True。

程序最后通过 Future 的 result() 方法来获取两个异步任务返回的结果。

读者可以自己运行此代码查看运行结果，这里不再演示。

当程序使用 Future 的 result() 方法来获取结果时，该方法会阻塞当前线程，如果没有指定 timeout 参数，当前线程将一直处于阻塞状态，直到 Future 代表的任务返回。

## 获取执行结果

前面程序调用了 Future 的 result() 方法来获取线程任务的运回值，但该方法会阻塞当前主线程，只有等到钱程任务完成后，result() 方法的阻塞才会被解除。

如果程序不希望直接调用 result() 方法阻塞线程，则可通过 Future 的 add_done_callback() 方法来添加回调函数，该回调函数形如 fn(future)。当线程任务完成后，程序会自动触发该回调函数，并将对应的 Future 对象作为参数传给该回调函数。

下面程序使用 add_done_callback() 方法来获取线程任务的返回值：

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time

# 定义一个准备作为线程任务的函数
def action(max):
    my_sum = 0
    for i in range(max):
        print(threading.current_thread().name + '  ' + str(i))
        my_sum += i
    return my_sum
# 创建一个包含 2 条线程的线程池
with ThreadPoolExecutor(max_workers=2) as pool:
    # 向线程池提交一个 task, 50 会作为 action()函数的参数
    future1 = pool.submit(action, 50)
    # 向线程池再提交一个 task, 100 会作为 action()函数的参数
    future2 = pool.submit(action, 100)
    def get_result(future):
        print(future.result())
    # 为 future1 添加线程完成的回调函数
    future1.add_done_callback(get_result)
    # 为 future2 添加线程完成的回调函数
    future2.add_done_callback(get_result)
    print('--------------')
```

上面主程序分别为 future1、future2 添加了同一个回调函数，该回调函数会在线程任务结束时获取其返回值。

主程序的最后一行代码打印了一条横线。由于程序并未直接调用 future1、future2 的 result() 方法，因此主线程不会被阻塞，可以立即看到输出主线程打印出的横线。接下来将会看到两个新线程并发执行，当线程任务执行完成后，get_result() 函数被触发，输出线程任务的返回值。

另外，由于线程池实现了上下文管理协议（Context Manage Protocol），因此，程序可以使用 with 语句来管理线程池，这样即可避免手动关闭线程池，如上面的程序所示。

此外，Exectuor 还提供了一个 `map(func, *iterables, timeout=None, chunksize=1)` 方法，该方法的功能类似于全局函数 map()，区别在于线程池的 map() 方法会为 iterables 的每个元素启动一个线程，以并发方式来执行 func 函数。这种方式相当于启动 len(iterables) 个线程，井收集每个线程的执行结果。

例如，如下程序使用 Executor 的 map() 方法来启动线程，并收集线程任务的返回值：

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time

# 定义一个准备作为线程任务的函数
def action(max):
    my_sum = 0
    for i in range(max):
        print(threading.current_thread().name + '  ' + str(i))
        my_sum += i
    return my_sum
# 创建一个包含 4 条线程的线程池
with ThreadPoolExecutor(max_workers=4) as pool:
    # 使用线程执行 map 计算
    # 后面元组有 3 个元素，因此程序启动 3 条线程来执行 action 函数
    results = pool.map(action, (50, 100, 150))
    print('--------------')
    for r in results:
        print(r)
```

上面程序使用 map() 方法来启动 3 个线程（该程序的线程池包含 4 个线程，如果继续使用只包含两个线程的线程池，此时将有一个任务处于等待状态，必须等其中一个任务完成，线程空闲出来才会获得执行的机会），map() 方法的返回值将会收集每个线程任务的返回结果。

运行上面程序，同样可以看到 3 个线程并发执行的结果，最后通过 results 可以看到 3 个线程任务的返回结果。

通过上面程序可以看出，使用 map() 方法来启动线程，并收集线程的执行结果，不仅具有代码简单的优点，而且虽然程序会以并发方式来执行 action() 函数，但最后收集的 action() 函数的执行结果，依然与传入参数的结果保持一致。也就是说，上面 results 的第一个元素是 action(50) 的结果，第二个元素是 action(100) 的结果，第三个元素是 action(150) 的结果。