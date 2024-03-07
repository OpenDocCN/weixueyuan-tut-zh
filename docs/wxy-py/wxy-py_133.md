# Python Redis 订阅发布功能详解

> 原文：[`www.weixueyuan.net/a/779.html`](http://www.weixueyuan.net/a/779.html)

前面都是通过查询的方法得到某个键的值，我们也可以用订阅的方法来得到某个键的值，而且仅在该值发生变化时才会被触发。其基本工作模式是：首先订阅某个频道的消息，如果该频道有消息发送过来，那么相应的处理函数就会被执行。

## 基本用法

实现订阅发布功能需要两个角色，一个是发布者，也就是消息发布者；另外一个是订阅者，就是消息接收和处理者。所以一般需要分别用代码实现这两个角色。

订阅者代码如下：

```

import redis                                    # 引入库
r = redis.Redis(host='10.20.17.13',port=6379)    # 建立连接
channel = 'test_channel1'                        # 定义频道名
sub_obj = r.pubsub()                            # 创建订阅对象
sub_obj.subscribe(channel)                        # 订阅到指定的频道上
sub_obj.parse_response()                        # 等待消息
while True:                                        # 无限循环
    msg = sub_obj.parse_response()                # 得到消息内容
    print(msg) 
```

运行后收到的消息是一个列表，显示如下：

[b'message', b'test_channel1', b'msg1']
[b'message', b'test_channel1', b'msg2']

发布者代码如下：

```

import redis
r = redis.Redis(host='10.20.17.13',port=6379)    # 建立连接
channel = 'test_channel1'                        # 频道名，要和订阅代码一致
r.publish(channel, "msg1")                        # 发送消息
r.publish(channel, "msg2")
```

我们可以像前面演示的那样编写两个程序，分别完成发布和订阅功能，也可以用多线程的方式，将它们写到一份代码里，但是这两个功能需要在两个线程中完成。

下面是合并后的代码，实现了和前面代码一样的功能，但是更有利于演示功能：

```

#encoding: utf-8
import redis
import time, _thread
def thread_entry(sub_obj):
    for i in range(2):
        msg = sub_obj.parse_response()
        print("Received Msg: %s" % msg)
r = redis.Redis(host='127.0.0.1',port=6379)
channel = 'test_channel1'
sub_obj = r.pubsub()
sub_obj.subscribe(channel)
sub_obj.parse_response()
_thread.start_new_thread(thread_entry, (sub_obj,))        # 启动订阅线程
time.sleep(1)
r.publish(channel, "msg1")
time.sleep(1)
r.publish(channel, "msg2")
time.sleep(1)
```

运行后的结果如下：

$ python pubsubThreads1.py
·Received Msg: [b'message', b'test_channel1', b'msg1']
Received Msg: [b'message', b'test_channel1', b'msg2']

前面这种方式看起来和消息队列没有太大的区别，就是一个任务负责发布消息，另外一个任务负责接收和处理消息。

但是它们还是有区别的，具体如下：
1) 消息队列中的消息一般只能被一个任务处理，而订阅方式可以有很多个接收消息的任务，它们都会接收到相同的消息，都能进行相关的处理。我们可以修改前面的多线程代码，启动多个订阅线程，然后发送一个消息，看看有多少个消息被接收并被处理。

下面是修改后的代码：

```

import redis
import time, _thread
def thread_entry(r, channel, sub_id):            # 订阅线程
    sub_obj = r.pubsub()                        # 创建对象
    sub_obj.subscribe(channel)                    # 订阅指定频道的消息
    sub_obj.parse_response()                    # 丢掉第一个消息
    for i in range(10):                            # 处理接收到的其他消息
        msg = sub_obj.parse_response()
        print("Thread [%d] Received Msg: %s" % (sub_id, msg))
r = redis.Redis(host='127.0.0.1',port=6379)    # 建立连接
channel = 'test_channel1'
time.sleep(1)
# 启动三个订阅线程，其 id 分别是 1、2 和 3
_thread.start_new_thread(thread_entry, (r, channel, 1))
_thread.start_new_thread(thread_entry, (r, channel, 2))
_thread.start_new_thread(thread_entry, (r, channel, 3))
time.sleep(2)
r.publish(channel, "msg1")                        # 发布第一个消息
time.sleep(2)
r.publish(channel, "msg2")                        # 发布第二个消息
time.sleep(2)
```

运行后的结果如下：

$ python multiSub1.py
Thread [1] Received Msg: [b'message', b'test_channel1', b'msg1']
Thread [3] Received Msg: [b'message', b'test_channel1', b'msg1']
Thread [2] Received Msg: [b'message', b'test_channel1', b'msg1']
Thread [1] Received Msg: [b'message', b'test_channel1', b'msg2']
Thread [2] Received Msg: [b'message', b'test_channel1', b'msg2']
Thread [3] Received Msg: [b'message', b'test_channel1', b'msg2']

可以看到每个消息都被三个线程分别接收和处理了，不会像消息队列那样，仅一个线程能够得到这个消息。

2) 消息队列中的消息一般一直在队列中，直到被处理任务读出。而订阅方式可以没有订阅者，这时发布的消息就没有任务进行处理，但是该消息不会一直保存在队列中，其会自动消失。

```

import redis
import time, _thread
def thread_entry(sub_obj):                        # 订阅线程
    while True:                                    # 一直接收消息
        msg = sub_obj.parse_response()            # 得到消息
        print("Received Msg: %s" % msg)    # 打印消息
r = redis.Redis(host='127.0.0.1',port=6379)    # 建立连接
channel = 'test_channel1'        # 定义频道
sub_obj = r.pubsub()            # 创建订阅对象，但是还没有和频道关联起来
# 现在没有接收者，发送的消息会丢失
r.publish(channel, "msg1")
sub_obj.subscribe(channel)        # 建立和频道的联系，以后该频道的消息不回丢了
sub_obj.parse_response()        # 丢掉第一个消息，这个是订阅成功的消息
time.sleep(1)
# 启动订阅线程
_thread.start_new_thread(thread_entry, (sub_obj,))
time.sleep(1)
r.publish(channel, "msg2")        # 发送第二个消息
time.sleep(1)
r.publish(channel, "msg3")        # 发送第三个消息
time.sleep(1)
```

运行后的结果如下：

$ python lostMsg.py
Received Msg: [b'message', b'test_channel1', b'msg2']
Received Msg: [b'message', b'test_channel1', b'msg3']

可以看到第一个消息 msg1 是没有被接收到的，原因就是在发送 msg1 时，没有人订阅该频道。

## 订阅多个频道

subscribe() 函数可以带有多个参数，表示其同时订阅多个频道。

下面演示了同时订阅两个频道的用法：

```

import redis                                    # 引入 redis 模块
import time, _thread                            # 引入时间和线程模块
def thread_entry(sub_obj):                        # 线程入口函数
    for i in range(10):                            # 循环 10 次
        msg = sub_obj.parse_response()            # 得到消息
        print("Received Msg: %s" % msg)            # 打印消息内容
r = redis.Redis(host='127.0.0.1',port=6379)    # 连接 Redis 服务器
channel1 = 'test_channel1'                        # 两个频道
channel2 = 'test_channel2'
sub_obj = r.pubsub()
sub_obj.subscribe(channel1, channel2)
sub_obj.parse_response()
sub_obj.parse_response()
_thread.start_new_thread(thread_entry, (sub_obj,))    # 启动线程
time.sleep(1)
r.publish(channel1, "msg1")                        # 在第一个频道发送消息
time.sleep(1)
r.publish(channel2, "msg2")                        # 在第二个频道发送消息
time.sleep(1)
```

运行后的结果如下：

$ python pubmultisubThreads1.py
Received Msg: [b'message', b'test_channel1', b'msg1']
Received Msg: [b'message', b'test_channel2', b'msg2']

## 频道使用通配符

如果使用 psubscribe() 来订阅某个频道，可以是使用通配符“*”，表示符合该模式的所有频道。例如可以用“test_channel*”来表示所有以 test_channel 开头的频道，这样发送给 test_channel1、test_channel2 等频道的消息它都可以收到。

下面是这种用法的一个演示：

```

import redis
import time, _thread
def thread_entry(r, channel, id):
    sub_obj = r.pubsub()
    group_channel = channel + "*"
    sub_obj.psubscribe(group_channel)
    sub_obj.parse_response()
    for i in range(10):
        msg = sub_obj.parse_response()
        print("Thread %d Received Msg: %s" % (id, msg))
r = redis.Redis(host='127.0.0.1',port=6379)
channel = 'test_channel'
# 启动订阅线程
_thread.start_new_thread(thread_entry, (r, channel, 1))
_thread.start_new_thread(thread_entry, (r, channel, 2))
_thread.start_new_thread(thread_entry, (r, channel, 3))
_thread.start_new_thread(thread_entry, (r, channel, 4))
time.sleep(1)
r.publish("test_channel1", "msg1")
time.sleep(1)
r.publish("test_channel2", "msg2")
time.sleep(1)
r.publish("test_channel3", "msg3")
time.sleep(1)
```

运行后的输出如下：

$ python groupsend.py
Thread 3 Received Msg: [b'pmessage', b'test_channel*', b'test_
     channel1', b'msg1']
Thread 1 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel1', b'msg1']
Thread 4 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel1', b'msg1']
Thread 2 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel1', b'msg1']
Thread 4 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel2', b'msg2']
Thread 2 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel2', b'msg2']
Thread 1 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel2', b'msg2']
Thread 3 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel2', b'msg2']
Thread 4 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel3', b'msg3']
Thread 2 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel3', b'msg3']
Thread 3 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel3', b'msg3']
Thread 1 Received Msg: [b'pmessage', b'test_channel*', b'test_
    channel3', b'msg3']