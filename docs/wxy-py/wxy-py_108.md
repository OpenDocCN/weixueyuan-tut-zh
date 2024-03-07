# UDP echo 服务器的建立（Python 实现）

> 原文：[`www.weixueyuan.net/a/719.html`](http://www.weixueyuan.net/a/719.html)

《TCP echo 服务器的建立》和《编写简单的 TCP 客户端》分别介绍了使用 TCP 协议编写 echo 服务的服务器端代码和客户端代码。本节将实现一个 UDP 版的 echo 服务。

虽然 TCP 和 UDP 区别很大，但是从 socket 接口来说它们并没有太大的区别，只是在 socket 创建时需要指定 socket 的不同类型，另外就是读取数据的接口时需要使用不同的接口。

在 UDP 中，最好不要使用 recv 接口，因为 UDP 是没有连接的，所以数据从哪里来是不确定的。也许第一段数据来自机器 A，第二段数据就来自机器 B。所以数据的来源就显得比较重要，使用 recv() 是无法得到发送方的地址信息的，而使用 recvfrom() 就可以得到发送方的地址信息。

发送数据也存在类似的情况。TCP 是有连接的，发送数据自然是将数据发送到另外一端。而 UDP 是没有连接的，所以在发送时需要指定接收人。send() 是不带接收人的，因为接收人在 bind() 时已经指定了。sendto() 是有接收人参数的，这也意味着一个 socket 可以先发送一段数据到机器 A，再发送一段数据到机器 B，这个是 TCP 无法实现的功能。

> TCP 型的 socket 只能在某两台机器之间发送数据。

另外一个区别是，现在服务器端不能用前面的连接是否存在来决定是否退出了。所以为了让服务器能够退出，需要给其发送命令 quit，服务器在接收到 quit 命令时即可自动退出，否则就一直坚守岗位，将所接收到的数据发送回去。

下面是服务器端的代码：

```

import socket
HOST = ''
PORT = 50007
    # 创建 socket 实例对象
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.bind((HOST, PORT))                    # 绑定本地端口号 50007
while True:                                # 循环读取数据
    data, sender = s.recvfrom(1024)        # 接收数据
    print('Got Data From', sender)                # 显示发送者的信息
    s_data = data.decode("utf-8")                # 解码
    print("Recv [%s] from " % s_data, sender)    # 显示数据内容
    s.sendto(data, sender)                # 将接收到的数据送回给发送者
    if s_data == "quit":                        # 如果是 quit，那么退出
        print("Got quit message, Echo Server Quit")
        break
s.close()                                        # 关闭 socket 接口
```

下面是客户端的代码。

```

import socket
HOST = '127.0.0.1'                                # 发送数据到本地
PORT = 50007
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
data = 'Hello, world'                            # 希望发送的字符串
b_data = data.encode("utf-8")                    # 编码
s.sendto(b_data, (HOST,PORT))                    # 发送数据
data, sender = s.recvfrom(1024)                # 等待服务区的返回数据
print('Received: ',  data, " From ", sender)    # 显示数据的发送方信息
data = 'quit'                                    # 发送 quit，让服务区退出
b_data = data.encode("utf-8")
s.sendto(b_data, (HOST,PORT))
s.close()                                        # 关闭 socket，节省资源
```