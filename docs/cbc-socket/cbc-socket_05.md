# 使用 socket()函数创建套接字

在 Linux 中，一切都是文件，除了文本文件、源文件、二进制文件等，一个硬件设备也可以被映射为一个虚拟的文件，称为设备文件。例如，stdin 称为标准输入文件，它对应的硬件设备一般是键盘，stdout 称为标准输出文件，它对应的硬件设备一般是显示器。对于所有的文件，都可以使用 read() 函数读取数据，使用 write() 函数写入数据。

“一切都是文件”的思想极大地简化了程序员的理解和操作，使得对硬件设备的处理就像普通文件一样。所有在 Linux 中创建的文件都有一个 int 类型的编号，称为文件描述符（File Descriptor）。使用文件时，只要知道文件描述符就可以。例如，stdin 的描述符为 0，stdout 的描述符为 1。

在 Linux 中，socket 也被认为是文件的一种，和普通文件的操作没有区别，所以在网络数据传输过程中自然可以使用与文件 I/O 相关的函数。可以认为，两台计算机之间的通信，实际上是两个 socket 文件的相互读写。

文件描述符有时也被称为文件句柄（File Handle），但“句柄”主要是 Windows 中术语，所以本教程中如果涉及到 Windows 平台将使用“句柄”，如果涉及到 Linux 平台将使用“描述符”。

## 在 Linux 下创建 socket

在 Linux 下使用 <sys/socket.h> 头文件中 socket() 函数来创建套接字，原型为：

```
int socket(int af, int type, int protocol);
```

1) af 为地址族（Address Family），也就是 IP 地址类型，常用的有 AF_INET 和 AF_INET6。AF 是“Address Family”的简写，INET 是“Inetnet”的简写。AF_INET 表示 IPv4 地址，例如 127.0.0.1；AF_INET6 表示 IPv6 地址，例如 1030::C9B4:FF12:48AA:1A2B。

大家需要记住`127.0.0.1`，它是一个特殊 IP 地址，表示本机地址，后面的教程会经常用到。

> 你也可以使用 PF 前缀，PF 是“Protocol Family”的简写，它和 AF 是一样的。例如，PF_INET 等价于 AF_INET，PF_INET6 等价于 AF_INET6。

2) type 为数据传输方式，常用的有 SOCK_STREAM 和 SOCK_DGRAM，在《[socket 是什么意思](http://c.biancheng.net/cpp/html/3029.html)》一节中已经进行了介绍。

3) protocol 表示传输协议，常用的有 IPPROTO_TCP 和 IPPTOTO_UDP，分别表示 TCP 传输协议和 UDP 传输协议。

有了地址类型和数据传输方式，还不足以决定采用哪种协议吗？为什么还需要第三个参数呢？

正如大家所想，一般情况下有了 af 和 type 两个参数就可以创建套接字了，操作系统会自动推演出协议类型，除非遇到这样的情况：有两种不同的协议支持同一种地址类型和数据传输类型。如果我们不指明使用哪种协议，操作系统是没办法自动推演的。

该教程使用 IPv4 地址，参数 af 的值为 PF_INET。如果使用 SOCK_STREAM 传输数据，那么满足这两个条件的协议只有 TCP，因此可以这样来调用 socket() 函数：

```
int tcp_socket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);  //IPPROTO_TCP 表示 TCP 协议
```

这种套接字称为 TCP 套接字。

如果使用 SOCK_DGRAM 传输方式，那么满足这两个条件的协议只有 UDP，因此可以这样来调用 socket() 函数：

```
int udp_socket = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);  //IPPROTO_UDP 表示 UDP 协议
```

这种套接字称为 UDP 套接字。

上面两种情况都只有一种协议满足条件，可以将 protocol 的值设为 0，系统会自动推演出应该使用什么协议，如下所示：

```
int tcp_socket = socket(AF_INET, SOCK_STREAM, 0);  //创建 TCP 套接字
int udp_socket = socket(AF_INET, SOCK_DGRAM, 0);  //创建 UDP 套接字
```

后面的教程中多采用这种简化写法。

## 在 Windows 下创建 socket

Windows 下也使用 socket() 函数来创建套接字，原型为：

```
SOCKET socket(int af, int type, int protocol);
```

除了返回值类型不同，其他都是相同的。Windows 不把套接字作为普通文件对待，而是返回 SOCKET 类型的句柄。请看下面的例子：

```
SOCKET sock = socket(AF_INET, SOCK_STREAM, 0);  //创建 TCP 套接字
```