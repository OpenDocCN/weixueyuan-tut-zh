# Python 多线程实现 socket 通信

前面章节对服务器端和客户端只是进行了简单的通信操作，即服务器端接受客户端的连接之后，向客户端输出一个字符串，而客户端也只是读取服务器端的字符串后就退出了。

在实际应用中，客户端则可能需要和服务器端保持长时间通信，即服务器端需要不断地读取客户端数据，并向客户端写入数据；客户端也需要不断地读取服务器端数据，并向服务器端写入数据。

由于 socket 的 recv() 方法在成功读取到数据之前，线程会被阻塞，程序无法继续执行。考虑到这个原因，服务器端应该为每个 socket 都单独启动一个线程，每个线程负责与一个客户端进行通信。

客户端读取服务器端数据的线程同样会被阻塞，所以系统应该单独启动一个线程，该线程专门负责读取服务器端数据。

现在考虑实现一个命令行界面的 C/S 聊天室应用，服务器端应该包含多个线程，每个 socket 对应一个线程，该线程负责从 socket 中读取数据（从客户端发送过来的数据），并将所读取到的数据向每个 socket 发送一次（将一个客户端发送过来的数据“广播”给其他客户端），因此需要在服务器端使用 list 来保存所有的 socket。

下面是服务器端的实现代码。该服务器端代码定义了一个 server_target() 函数，该函数将会作为线程执行的 target，负责处理每个 socket 的数据通信。

```
import socket
import threading

# 定义保存所有 socket 的列表
socket_list = []
# 创建 socket 对象
ss = socket.socket()
# 将 socket 绑定到本机 IP 和端口
ss.bind(('192.168.1.88', 30000))
# 服务端开始监听来自客户端的连接
ss.listen()
def read_from_client(s):
    try:
        return s.recv(2048).decode('utf-8')
    # 如果捕获到异常，则表明该 socket 对应的客户端已经关闭
    except:
        # 删除该 socket
        socket_list.remove(s);      # ①
def server_target(s):
    try:
        # 采用循环不断地从 socket 中读取客户端发送过来的数据
        while True:
            content = read_from_client(s)
            print(content)
            if content is None:
                break
            for client_s in socket_list:
                client_s.send(content.encode('utf-8'))
    except e:
        print(e.strerror)
while True:
    # 此行代码会阻塞，将一直等待别人的连接
    s, addr = ss.accept()
    socket_list.append(s)
    # 每当客户端连接后启动一个线程为该客户端服务
    threading.Thread(target=server_target, args=(s, )).start()
```

上面实现的服务器端主程序只负责接收客户端 socket 的连接请求，每当客户端 socket 连接进来之后，程序都将对应的 socket 加入 socket_list 列表中保存，并为该 socket 启动一个线程，该线程负责处理该 socket 所有的通信任务，如程序中最后 3 行代码所示。

代表服务器端线程执行体的 server_target() 函数则不断地读取客户端数据。程序使用 read_from_client() 函数来读取客户端数据，如果在读取数据过程中捕获到异常，则表明该 socket 对应的客户端 socket 出现了问题（到底什么问题不用深究，反正不正常），程序就将该 socket 从 socket_list 列表中删除，如 read_from_client() 函数中的 ① 号代码所示。

当服务器端线程读取到客户端数据之后，程序遍历 socket_list 列表，并将该数据向 socket_list 列表中的每个 socket 发送一次（该服务器端线程把从 socket 中读取到的数据向 socket_list 列表中的每个 socket 转发一次），如 server_target() 函数中的代码所示。

每个客户端都应该包含两个线程，其中一个负责读取用户的键盘输入内容，并将用户输入的数据输出到 socket 中，另一个负责读取 socket 中的数据（从服务器端发送过来的数据），并将这些数据打印输出。由程序的主线程负责读取用户的键盘输入内容，由新线程负责读取 socket 数据。

```
import socket
import threading

# 创建 socket 对象
s = socket.socket()
# 连接远程主机
s.connect(('192.168.1.88', 30000))
def read_from_server(s):
    while True:
        print(s.recv(2048).decode('utf-8'))
# 客户端启动线程不断地读取来自服务器的数据
threading.Thread(target=read_from_server, args=(s, )).start()   # ①
while True:
    line = input('')
    if line is None or line == 'exit':
        break
    # 将用户的键盘输入内容写入 socket
    s.send(line.encode('utf-8'))
```

上面程序中的主线程读取到用户的键盘输入内容后，将该内容发送到 socket 中（实际上就是把数据发送给服务器端）。

此外，当主线程的 socket 连接到服务器端之后，以 read_from_server() 函数为 target 启动了新线程来处理 socket 通信，如程序中 ① 号代码所示。read_from_server() 函数使用死循环读取 socket 中的数据（就是来自服务器端的数据），并将这些内容在控制台打印出来，如 read_from_server() 函数中的代码所示。

先运行上面的 MyServer 程序，该程序运行后只是作为服务器，看不到任何输出信息。再运行多个 MyClient 程序（相当于启动多个聊天室客户端登录该服务器），接下来可以在任何一个客户端通过键盘输入一些内容，然后按回车键，即可在所有客户端（包括自己）的控制台中收到刚刚输入的内容，这就粗略地实现了一个 C/S 结构的聊天室应用。