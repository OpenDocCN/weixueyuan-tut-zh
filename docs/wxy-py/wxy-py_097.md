# Python 线程安全（同步锁机制）

> 原文：[`www.weixueyuan.net/a/674.html`](http://www.weixueyuan.net/a/674.html)

所谓线程安全,就是说对某个资源的访问在多线程状态下和单线程状态下得到相同的结果，结果不会受到线程调度等因素的影响。

例如下面这段代码，在函数 operate_resource() 中为 g_list 添加一个新元素，新元素的值为 g_list 最后一个元素的值加 1。

```

g_list = []
def operate_resource():
        global g_list
        ele_num = len(g_list)
        if ele_num == 0:
                g_list.append(1)
        else:
                last_val = g_list[ele_num-1]
                new_last_val = last_val + 1
                g_list.append(new_last_val)
```

如果在单个线程中不停地调用 operate_resource()，那么 g_list 列表会不停地生长，而且值是依次加一的。但是如果同时启动两个线程，每个线程都在循环调用 operate_resource()，那么便不能保证得到的 g_list 的值是每次都加一的。

上面的例子中，假定这两个线程分别为 A 和 B。现在 g_list 为空，在 A 执行到第 4 行时，其得到 ele_num=0，所以其下一句是通过判断而执行第 6 句，假定在这时发生了线程切换，现在 A 线程被调度出去，B 被激活了。线程 B 也执行到第 4 行代码，同样的 B 也得到 ele_num=0，所以其也会执行第 6 行代码。这样最后得到的 g_list 会是 [1,1] 而不是 [1,2]，这不是预期的结果。

可以用下面的代码来完整地演示这个问题。

```

import sys, time                        # 引入时间库
if sys.version_info.major == 2:         # Python 2
    import thread
else:                                   # Python 3
    import _thread as thread
g_list = []
def operate_resource():                 # 操作资源
    global g_list
    ele_num = len(g_list)
    if ele_num == 0:
        g_list.append(1)
    else:
        last_val = g_list[ele_num-1]
        new_last_val = last_val + 1
        g_list.append(new_last_val)
def thread_entry(id, round):             # 线程入口函数
    while round > 0:
        operate_resource()
        round = round - 1
    print("Thread %d Finished" % id)
def start_threads():                     # 启动线程
    global g_list
    t1 = thread.start_new_thread(thread_entry, (1, 10000))
    t2 = thread.start_new_thread(thread_entry, (2, 10000))
    time.sleep(10)
    print("Check the Result")
    loc = 0             # 开始位置，其依次检查是否满足 g_list[loc] = (loc + 1)
    while loc < 20000:
        if g_list[loc] != (loc + 1):            # 该值与预期的不同
            print("Error: at %d" % (loc+1))
            break
        loc = loc + 1
if __name__=='__main__':
    start_threads()
```

运行结果如下：

$ python threadSafe1.py      # 运行脚本
Thread 2 Finished
Thread 1 Finished
Check the Result
Error: at 6488
$ python threadSafe1.py      # 再运行一次
Thread 2 Finished
Thread 1 Finished
Check the Result
Error: at 3666

可以看到最后得到的结果不是预期的。当然这个错误的数据有点随机性，就是说有可能是第 1 个元素发生了错误，也可能是第 100 个元素发生了错误。例如上面的例子，其运行了两次，一次是第 6488 个元素发生了错误，一次是第 3666 个元素发生了错误。读者在运行该程序时还可能得到不同的错误位置。

造成该错误的原因是某些操作应该是保证原子性的却没有保证。如上面这个例子，operate_resource() 应该是要保证原子性的，就是说在 operate_resource() 执行过程中其所使用的资源不能再次被其他线程使用，即线程 A 在操作 g_list 的过程中，其他线程不能使用 g_list。

解决办法是使用锁，基本用法如下：

1.  得到锁 A，阻止其他线程使用特定数据。
2.  使用共享的对象。
3.  释放锁 A，允许其他线程使用特定数据。

这样在任意时刻，只有一个线程在执行上面的第二步。

该方法和同步的区别是，其仅使用一个锁，所以可能出现某个线程 A 多次连续得到锁。而同步则是依次交互执行，一般不会出现某个线程连续多次得到执行机会。

现在修改上面的代码，在访问数据之前加上锁，在访问结束后释放锁。代码如下：

```

import sys, time
if sys.version_info.major == 2:
    import thread
else:
    import _thread as thread
mutex_lock = thread.allocate_lock()
g_list = []
def operate_resource():
    global g_list
    ele_num = len(g_list)
    if ele_num == 0:
        g_list.append(1)
    else:
        last_val = g_list[ele_num-1]
        new_last_val = last_val + 1
        g_list.append(new_last_val)
def thread_entry(id, round):··         # 线程入口函数
    while round > 0:                   # 循环 round 次
        mutex_lock.acquire()
        operate_resource()
        mutex_lock.release()
        round = round - 1
    print("Thread %d Finished" % id)
def start_threads():                   # 启动线程
    global g_list
    t1 = thread.start_new_thread(thread_entry, (1, 10000))
    t2 = thread.start_new_thread(thread_entry, (2, 10000))
    time.sleep(10)
    print("Check the Result")
    loc = 0
    while loc < 20000:
        if g_list[loc] != (loc + 1):
            print("Error: at %d" % (loc+1))
            break
        loc = loc + 1
    print("All Passed")
if __name__=='__main__':
    start_threads()
```

再次运行程序，没有错误出现。

$ python threadSafe2.py
Thread 1 Finished
Thread 2 Finished
Check the Result
All Passed

> 这里可以用的锁有很多，除了上面例子使用的 thread.Lock，threading.Lock() 和 threading.RLock() 也都可以使用。