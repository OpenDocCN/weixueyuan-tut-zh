# TCP echo 服务器的建立（Python 实现）

> 原文：[`www.weixueyuan.net/a/716.html`](http://www.weixueyuan.net/a/716.html)

本节我们将编写一个简单的 echo 服务器。该服务器等待用户的连接，连接成功后，它将接收到的用户的任何输入都原封不动地回送给客户端，直到用户关闭了连接，此时服务器自动退出。

这个例子使用了 TCP 协议，使用的协议是在创建 socket 时指定的，方法如下：

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

第一个参数 socket.AF_INET 表示使用 TCP/IP 协议族，第二个参数 socket.SOCK_STREAM 表示使用 TCP 而不是 UDP。

我们在服务器端使用端口 50007。对于服务器来说，端口是确定的，如果不确定，客户端就不知道将消息发送到哪里。IP 地址可以不用填写，因为程序可以从操作系统中得到自己的 IP 地址信息。我们使用 bind() 来确定服务器使用的端口和 IP 地址。

s.bind((HOST, PORT))

listen() 函数是用来等待客户端连接的。一般情况下，服务器是先运行起来，并且一直处于等待状态，直到有客户端发起连接。

s.listen(10)

参数 10 表示等待队列为 10，也就是说，如果同时有 12 个客户端发起连接，那么第 11 个和第 12 个会被拒绝。这些被拒绝的客户端的 connect() 函数会返回失败信息。

accept() 函数用来接收客户端的连接请求。该函数返回客户端的 Ip 地址和端口号信息，并且返回一个新的 socket 对象。

conn, addr = s.accept()

使用 accept() 返回的新的 socket 对象，既可以读数据，也可以写数据。读数据相当于是从客户端得到它们发送的数据，写数据则相当于是给客户端发送数据。

data = conn.recv(1024)
conn.sendall(data)

读数据时可以指定缓存大小，就是说如果客户端发送的数据特别多，可以分几次读取，每次最多读取指定的字节数。如上面的例子中，指定了当前最多读入 1024 个字节的数据，如果客户端发送的数据大于 1024B，则需要多次读入。

最后关闭 socket 以释放资源。注意，服务器有两个 socket，一个用来接收连接请求的，一个用来发送数据。所以可以看到下面两个关闭语句：

conn.close()
s.close()

下面是完整的代码：

```

import socket                    # 使用的 socket 接口
HOST = ''                         # 本地地址和端口
PORT = 50007
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)    # 创建 socket
s.bind((HOST, PORT))            # 绑定到本地 IP 和端口
s.listen(10)                    # 等待用户请求连接
conn, addr = s.accept()            # 接收用户连接请求
print('Connected From', addr)    # 显示用户的地址信息
while True:                        # 一直接收用户数据并原封不动地返回
    data = conn.recv(1024)        # 读入数据
    if not data:                 # 如果读入失败，如说用户关闭了连接
        break                    # 跳出循环
    conn.send(data)                # 将接收到的数据返回给用户
conn.close()                    # 关闭读写数据连接
s.close()                        # 关闭连接请求 socket
```