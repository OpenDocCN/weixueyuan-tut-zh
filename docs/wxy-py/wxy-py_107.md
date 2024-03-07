# 编写简单的 TCP 客户端

> 原文：[`www.weixueyuan.net/a/718.html`](http://www.weixueyuan.net/a/718.html)

在《TCP echo 服务器的建立》一节的基础上，我们还需要一个客户端，这样才能实现数据交互。

本节将编写一个这样的客户端，它和前面介绍的服务器建立连接，并发送一串简单的数据，然后读取服务器的返回数据。

首先需要创建一个 socket 对象。和服务器端一样，也是指定使用 TCP。注意，客户端和服务器端的传输层协议必须一致，不能一个使用 TCP，另一个使用 UDP。

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

然后使用 connect() 函数连接到服务器，需要指定服务的 IP 地址和端口号。这两个值都要和服务器端匹配，如服务器使用 50007 端口，那么客户端一定也要指定该端口。

s.connect((HOST, PORT))

在成功后便可以读写数据了，客户端读数据就是接收服务器端发送过来的数据，客户端写数据就是给服务器端发送数据。读数据时同样可以指定缓存区的大小，方法如下：

s.sendall(b_data)
data = s.recv(1024)

在使用完成后可以使用 close() 函数来关闭 socket，以释放资源。

s.close()

下面是完整的代码：

```

import socket
HOST = '127.0.0.1'                    # 服务器地址
PORT = 50007                          # 服务器端口
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))                # 连接服务器
data = 'Hello, world'
b_data = data.encode("utf-8")
s.send(b_data)                        # 发送数据
data = s.recv(1024)                    # 接收回应
s.close()                            # 关闭连接，释放资源
print('Received: ', data)
```

在启动服务器端代码后，启动客户端代码，输出如下：

$ python echo-client1.py
Received:  b'Hello, world':

在客户端退出时，服务器也会自动退出。