# Python Event 实现线程通信

Event 是一种非常简单的线程通信机制，一个线程发出一个 Event，另一个线程可通过该 Event 被触发。

Event 本身管理一个内部旗标，程序可以通过 Event 的 set() 方法将该旗标设置为 True，也可以调用 clear() 方法将该旗标设置为 False。程序可以调用 wait() 方法来阻塞当前线程，直到 Event 的内部旗标被设置为 True。

Event 提供了如下方法：

*   is_set()：该方法返回 Event 的内部旗标是否为 True。
*   set()：该方法将会把 Event 的内部旗标设置为 True，并唤醒所有处于等待状态的线程。
*   clear()：该方法将 Event 的内部旗标设置为 False，通常接下来会调用 wait() 方法来阻塞当前线程。
*   wait(timeout=None)：该方法会阻塞当前线程。

下面程序示范了 Event 最简单的用法：

```
import threading
import time

event = threading.Event()
def cal(name):
    # 等待事件，进入等待阻塞状态
    print('%s 启动' % threading.currentThread().getName())
    print('%s 准备开始计算状态' % name)
    event.wait()    # ①
    # 收到事件后进入运行状态
    print('%s 收到通知了.' % threading.currentThread().getName())
    print('%s 正式开始计算！'% name)
# 创建并启动两条，它们都会①号代码处等待
threading.Thread(target=cal, args=('甲', )).start()
threading.Thread(target=cal, args=("乙", )).start()
time.sleep(2)    #②
print('------------------')
# 发出事件
print('主线程发出事件')
event.set()
```

上面程序以 cal() 函数为 target，创建并启动了两个线程。由于 cal() 函数在 ① 号代码处调用了 Event 的 wait()，因此两个线程执行到 ① 号代码处都会进入阻塞状态；即使主线程在 ② 号代码处被阻塞，两个子线程也不会向下执行。

直到主程序执行到最后一行，程序调用了 Event 的 set() 方法将 Event 的内部旗标设直为 True，并唤醒所有等待的线程，这两个线程才能向下执行。

运行上面程序，将看到如下输出结果：

Thread-1 启动
甲 准备开始计算状态
Thread-2 启动
乙 准备开始计算状态
------------------
主线程发出事件
Thread-1 收到通知了.
Thread-2 收到通知了.
甲 正式开始计算！
乙 正式开始计算！

上面程序还没有使用 Event 的内部旗标，如果结合 Event 的内部旗标，同样可实现前面的 Account 的生产者-消费者效果：存钱线程（生产者）存钱之后，必须等取钱线程（消费者）取钱之后才能继续向下执行。

Event 实际上优点类似于 Condition 和旗标的结合体，但 Event 本身并不带 Lock 对象，因此如果要实现线程同步，还需要额外的 Lock 对象。

下面是使用 Event 改写后的 Account：

```
import threading

class Account:
    # 定义构造器
    def __init__(self, account_no, balance):
        # 封装账户编号、账户余额的两个成员变量
        self.account_no = account_no
        self._balance = balance
        self.lock = threading.Lock()
        self.event = threading.Event()
    # 因为账户余额不允许随便修改，所以只为 self._balance 提供 getter 方法
    def getBalance(self):
        return self._balance
    # 提供一个线程安全的 draw()方法来完成取钱操作
    def draw(self, draw_amount):
        # 加锁
        self.lock.acquire()
        # 如果 Event 内部旗标为 True，表明账户中已有人存钱进去
        if self.event.is_set():
            # 执行取钱操作
            print(threading.current_thread().name
                + " 取钱:" +  str(draw_amount))
            self._balance -= draw_amount
            print("账户余额为：" + str(self._balance))
            # 将 Event 内部旗标设为 False
            self.event.clear()
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
        else:
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
    def deposit(self, deposit_amount):
        # 加锁
        self.lock.acquire()
        # 如果 Event 内部旗标为 False，表明账户中还没有人存钱进去
        if not self.event.is_set():
            # 执行存款操作
            print(threading.current_thread().name\
                + " 存款:" +  str(deposit_amount))
            self._balance += deposit_amount
            print("账户余额为：" + str(self._balance))
            # 将 Event 内部旗标设为 True
            self.event.set()
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
        else:
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
```