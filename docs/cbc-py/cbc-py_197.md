# Python Timer 定时器：控制函数在特定时间执行

Thread 类有一个 Timer 子类，该子类可用于控制指定函数在特定时间内执行一次。例如如下程序：

```
from threading import Timer

def hello():
    print("hello, world")
# 指定 10 秒后执行 hello 函数
t = Timer(10.0, hello)
t.start()
```

上面程序使用 Timer 控制 10s 后执行 hello 函数。

需要说明的是，Timer 只能控制函数在指定时间内执行一次，如果要使用 Timer 控制函数多次重复执行，则需要再执行下一次调度。

如果程序想取消 Timer 的调度，则可调用 Timer 对象的 cancel() 函数。例如，如下程序每 1s 输出一次当前时间：

```
from threading import Timer
import time

# 定义总共输出几次的计数器
count = 0
def print_time():
    print("当前时间：%s" % time.ctime())
    global t, count
    count += 1
    # 如果 count 小于 10，开始下一次调度
    if count < 10:
        t = Timer(1, print_time)
        t.start()
# 指定 1 秒后执行 print_time 函数
t = Timer(1, print_time)
t.start()
```

上面程序开始运行后，程序控制 1s 后执行 print_time() 函数。print_time() 函数中的代码会进行判断，如果 count 小于 10，程序再次使用 Timer 调度 1s 后执行 print_time() 函数，这样就可以控制 print_time() 函数多次重复执行。

在上面程序中，由于只有当 count 小于 10 时才会使用 Timer 调度 1s 后执行 print_time() 函数，因此该函数只会重复执行 10 次。