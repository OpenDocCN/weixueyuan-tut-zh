# Python Redis 管道模式

> 原文：[`www.weixueyuan.net/a/782.html`](http://www.weixueyuan.net/a/782.html)

我们知道所有对 Redis 的操作都需要发送消息给服务器，然后等待服务器处理，在服务器完成后才能执行下一个操作。

而使用管道（pipelining）模式就可以不用等待服务器处理完之后再进行下一个操作。这样可以将自己要执行的操作像脚本文件一样打包发送给服务器，服务器会依次执行。由于服务器其实是单线程模式运行的，所以不会出现第 3 个请求在第 1 个请求结束之前就开始的情况，这样就不会出现错误结果。这种做法主要是节省了中间的等待时间。

管道模式要求申请一个管道（pipeline）对象，然后在该对象中输入要执行的命令，最后启动管道，就是向服务器发送请求。

```

>>> r = redis.Redis(host='127.0.0.1',port=6379)    # 连接服务器
>>> pipe = r.pipeline()                                # 创建管道
>>> pipe.set('key1', 'val1')                        # 设定值
Pipeline<ConnectionPool<Connection<host=127.0.0.1,port=6379,db=0>>>
>>> pipe.set('key2', 'val2')
Pipeline<ConnectionPool<Connection<host=127.0.0.1,port=6379,db=0>>>
>>> pipe.set('key3', 'val3')
Pipeline<ConnectionPool<Connection<host=127.0.0.1,port=6379,db=0>>>
>>> pipe.execute()
[True, True, True]
```

从前面的输出可以看到管道执行操作之后返回值还是自己，所以可以用链式写法如下：

```

>>> r = redis.Redis(host='127.0.0.1',port=6379)    # 连接服务器
>>> pipe = r.pipeline()
>>> pipe.set('key1', 'val1').set('key2', 'val2').set('key3', 'val3')
Pipeline<ConnectionPool<Connection<host=127.0.0.1,port=6379,db=0>>>
>>> pipe.execute()
[True, True, True]
```

管道默认是原子性的，就是在执行 execute() 的过程中，其他的请求是不会被执行的，就算是其他的客户端在这时发送了请求，这个新的请求也只能等待 execute() 执行完毕后才可以开始执行。