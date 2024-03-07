# Python socket shutdown 方法详解

前面在介绍服务器端和客户端通信时，总是以一个 bytes 对象作为通信的最小数据单位的，服务器端在处理信息时也是针对每个 bytes 进行的。在一些协议中，通信的数据单位可能需要多个 bytes 对象，在这种情况下，就需要解决一个问题，socket 如何表示输出数据已经结束？

如果要表示输出数据已经结束，则可以通过关闭 socket 来实现。但如果彻底关闭了 socket，则会导致程序无法再从该 socket 中读取数据。

在这种情况下，socket 提供了一个 shutdown(how) 关闭方法，该方法可以只关闭 socket 的输入或输出部分，用以表示输出数据已经发送完成。

shutdown 方法的 how 参数接受如下参数值：

*   SHUT_RD：关闭 socket 的输入部分，程序还可通过该 socket 输出数据。
*   SHUT_WR： 关闭该 socket 的输出部分，程序还可通过该 socket 读取数据。
*   SHUT_RDWR：全关闭。该 socket 既不能读取数据，也不能写入数据。

当调用 shutdown() 方法关闭 socket 的输入或输出部分之后，该 socket 处于“半关闭”状态。

即使一个 socket 实例在调用 shutdown() 方法时专入了 SHUT_RDWR 参数，该 socket 也依然没有被彻底清理（与 close() 方法不同），只是该 socket 既不能输出数据，也不能读取数据了。

下面程序示范了 shutdown() 方法的用法。在该程序中服务器端先向客户端发送多条数据，当数据发送完成后，该 socket 对象调用 shutdown() 方法来关闭输出部分，表明数据发送结束在关闭输出部分之后，依然可以从 socket 中读取数据。

```
import socket

# 创建 socket 对象
s = socket.socket()
# 将 socket 绑定到本机 IP 和端口
s.bind(('192.168.1.88', 30000))
# 服务端开始监听来自客户端的连接
s.listen()
# 每当接收到客户端 socket 的请求时，该方法返回对应的 socket 和远程地址
skt, addr = s.accept()
skt.send("服务器的第一行数据".encode('utf-8'))
skt.send("服务器的第二行数据".encode('utf-8'))
# 关闭 socket 的输出，表明输出数据已经结束
skt.shutdown(socket.SHUT_WR)
while True:
    # 从 socket 读取数据
    line = skt.recv(2048).decode('utf-8')
    if line is None or line == '':
        break
    print(line)
skt.close()
s.close()
```

上面程序中，第 14 行代码关闭了 socket 的输出部分，此时该 socket 并未被彻底关闭，程序只是不能向该 socket 中写入数据了，但依然可以从该 socket 中读取数据。

本程序的客户端代码比较简单，它先读取服务器端返回的数据，然后向服务器端输出一些内容，故此处不再赘述。

当调用 socket 的 shutdown() 方法关闭了输入或输出部分之后，该 socket 无法再次打开输入或输出部分，因此这种做法通常不适合保持持久通信状态的交互式应用，只适用于一站式的通信协议，例如 HTTP 协议，即客户端连接到服务器端，开始发送请求数据，当发送完成后无须再次发送数据，只需要读取服务器端的响应数据即可，当读取响应数据完成后，该 socket 连接就被完全关闭了。