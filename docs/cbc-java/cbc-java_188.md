# Java UDP 通信：Java DatagramSocket 类和 DatagramPacket 类

在 TCP/IP 协议的传输层除了一个 TCP 协议之外，还有一个 UDP 协议。UDP 协议是用户数据报协议的简称，也用于网络数据的传输。虽然 UDP 协议是一种不太可靠的协议，但有时在需要较快地接收数据并且可以忍受较小错误的情况下，UDP 就会表现出更大的优势。

下面是在 Java 中使用 UDP 协议发送数据的步骤。

1.  使用 DatagramSocket() 创建一个数据包套接字。
2.  使用 DatagramPacket() 创建要发送的数据包。
3.  使用 DatagramSocket 类的 send() 方法发送数据包。

接收 UDP 数据包的步骤如下：

*   使用 DatagramSocket 创建数据包套接字，并将其绑定到指定的端口。
*   使用 DatagramPacket 创建字节数组来接收数据包。
*   使用 DatagramPacket 类的 receive() 方法接收 UDP 包。

## DatagramPacket 类

java.net 包中的 DatagramPacket 类用来表示数据报包，数据报包用来实现无连接包投递服务。每条报文仅根据该包中包含的信息从一台机器路由到另一台机器。从一台机器发送到另一台机器的多个包可能选择不同的路由，也可能按不同的顺序到达。表 1 和表 2 简单介绍了 DatagramPacket 的构造方法和常用方法。

表 1 DatagramPacket 的构造方法

| 构造方法 | 说明 |
| DatagramPacket(byte[] buf,int length) | 构造 DatagramPacket，用来接收长度为 length 的数据包。 |
| DatagramPacket(byte[] buf,int offset, int length) | 构造 DatagramPacket，用来接收长度为 length 的包，在缓 冲区中指定了偏移量。 |
| DatagramPacket(byte[] buf,int length, InetAddress address,int port) | 构造 DatagramPacket，用来将长度为 length 的包发送到指 定主机上的指定端口。 |
| DatagramPacket(byte[] buf,int length, SocketAddress address) | 构造数据报包，用来将长度为 length 的包发送到指定主机上 的指定端口。 |
| DatagramPacket(byte[] buf,int offset, int length,InetAddress address,int port) | 构造 DatagramPacket，用来将长度为 length 偏移量为 offset 的包发送到指定主机上的指定端口。 |
| DatagramPacket(byte[] buf,int offset, int length,SocketAddress address) | 构造数据报包，用来将长度为 length、偏移量为 offset 的包发 送到指定主机上的指定端口。 |

表 2 DatagramPacket 的常用方法

| 方法 | 说明 |
| InetAddress getAddress() | 返回某台机器的 IP 地址，此数据报将要发往该机器或者 从该机器接收。 |
| byte[] getData() | 返回数据缓冲区。 |
| int getLength() | 返回将要发送或者接收的数据的长度。 |
| int getOffset() | 返回将要发送或者接收的数据的偏移量。 |
| int getPort() | 返回某台远程主机的端口号，此数据报将要发往该主机或 者从该主机接收。 |
| getSocketAddress() | 获取要将此包发送或者发出此数据报的远程主机的 SocketAddress（通常为 IP 地址+端口号）。 |
| void setAddress(InetAddress addr) | 设置要将此数据报发往的目的机器的 IP 地址。 |
| void setData(byte[] buf) | 为此包设置数据缓冲区。 |
| void setData(byte[] buf,int offset, int length) | 为此包设置数据缓冲区。 |
| void setLength(int length) | 为此包设置长度。 |
| void setPort(int port) | 设置要将此数据报发往的远程主机的端口号。 |
| void setSocketAddress(SocketAddress address) | 设置要将此数据报发往的远程主机的 SocketAddress（通常为 IP 地址+端口号）。 |

## DatagramSocket 类

DatagramSocket 类用于表示发送和接收数据报包的套接字。数据报包套接字是包投递服务的发送或接收点。每个在数据报包套接字上发送或接收的包都是单独编址和路由的。从一台机器发送到另一台机器的多个包可能选择不同的路由，也可能按不同的顺序到达。

DatagramSocket 类的常用构造方法如表 3 所示。

表 3 DatagramSocket 的构造方法

| 构造方法 | 说明 |
| DatagramSocket() | 构造数据报包套接字并将其绑定到本地主机上任何可用的端口。 |
| DatagramSocket(int port) | 创建数据报包套接字并将其绑定到本地主机上的指定端口。 |
| DatagramSocket(int portJnetAddress addr) | 创建数据报包套接字，将其绑定到指定的本地地址。 |
| DatagramSocket(SocketAddress bindaddr) | 创建数据报包套接字，将其绑定到指定的本地套接字地址。 |

DatagramSocket 类的常用方法如表 4 所示。

表 4 DatagramSocket 的常用方法

| 方法 | 说明 |
| void bind(SocketAddress addr) | 将此 DatagramSocket 绑定到特定的地址和端口。 |
| void close() | 关闭此数据报包套接字。 |
| void connect(InetAddress address,int port) | 将套接字连接到此套接字的远程地址。 |
| void connect(SocketAddress addr) | 将此套接子连接到远程套接子地址（IP 地址+端口号）。 |
| void disconnect() | 断开套接字的连接。 |
| InetAddress getInetAddress() | 返回此套接字连接的地址。 |
| InetAddress getLocalAddress() | 获取套接字绑定的本地地址。 |
| int getLocalPort() | 返回此套接字绑定的本地主机上的端口号。 |
| int getPort() | 返回此套接字的端口。 |

#### 例 1

编写 UDP 程序，要求客户端程序可以向服务器端发送多条数据，服务器端程序可以接收客户端发送的多条数据并将其信息输出在控制台，主要步骤如下所示。

(1) 创建一个类作为客户端，在 main() 方法定义一个 DatagramSocket 对象和一个 DatagramPacket 对象并初始化为 null。然后再定义一个 InetAddress 对象和一个端口号并分别进行初始化，代码如下所示。

```
public static void main(String[] args)
{
    DatagramSocket ds=null;
    DatagramPacket dpSend=null;
    InetAddress ia=InetAddress.getByName("127.0.0.1");
    int port=3021;
}
```

(2) 使用 DatagramSocket 的 send(DatagramPacket p) 方法向服务器端发送数据报包，使用循环的方式完成 5 次数据的发送，每发送 1 次数据线程休眠 1000 毫秒，数据发送完毕调用 close() 方法，关闭 DatagramSocket 对象，代码如下。

```
try
{
    ds=new DatagramSocket();
    for(int i=0;i<5;i++)
    {
        byte[] data=("我是 UDP 客户端"+i).getBytes();
        dpSend=new DatagramPacket(data,data.length,ia,port);
        ds.send(dpSend);
        Thread.sleep(1000);
    }
    ds.close();
}
catch(IOException | InterruptedException e)
{
    // TODO 自动生成的 catch 块
    e.printStackTrace();
}   
```

(3) 创建一个类作为服务器端，在 main() 方法中定义一个 DatagramSocket 对象和一个 DatagramPacket 对象并初始化为 null，再定义一个端口号，代码如下所示。

```
public static void main(String[] args)
{
    DatagramSocket ds=null;
    DatagramPacket dpReceive=null;
    int port=3021;
}
```

(4) 如果成功连接到 UDP 服务器则输出“UDP 服务器已启动。。。”。循环接收客户端发送的数据，并将其发送的内容以及 IP 地址等信息输出到控制台，代码如下所示。

```
try
{
    ds=new DatagramSocket(port);
    System.out.println("UDP 服务器已启动。。。");
    byte[] b=new byte[1024];
    while(ds.isClosed()==false)
    {
        dpReceive=new DatagramPacket(b, b.length);
        try
        {
            ds.receive(dpReceive);
            byte[] Data=dpReceive.getData();
            int len=Data.length;
            System.out.println("UDP 客户端发送的内容是：" + new String(Data, 0, len).trim());
            System.out.println("UDP 客户端 IP：" + dpReceive.getAddress());
            System.out.println("UDP 客户端端口：" + dpReceive.getPort());
        }
        catch(IOException e)
        {
            e.printStackTrace();
        }
    }
    }
    catch(SocketException e1)
    {
        // TODO 自动生成的 catch 块
        e1.printStackTrace();
    }
}
```

(5) 最后关闭 DatagramSocket 对象。执行服务器端程序，运行结果如下所示。

```
UDP 服务器已启动。。。
```

(6) 再运行客户端程序，控制台的输出结果如下所示。

```
UDP 客户端发送的内容是：我是 UDP 客户端 0
UDP 客户端 IP：/127.0.0.1
UDP 客户端端口：53472
UDP 客户端发送的内容是：我是 UDP 客户端 1
UDP 客户端 IP：/127.0.0.1
UDP 客户端端口：53472
UDP 客户端发送的内容是：我是 UDP 客户端 2
UDP 客户端 IP：/127.0.0.1
UDP 客户端端口：53472
UDP 客户端发送的内容是：我是 UDP 客户端 3
UDP 客户端 IP：/127.0.0.1
UDP 客户端端口：53472
UDP 客户端发送的内容是：我是 UDP 客户端 4
UDP 客户端 IP：/127.0.0.1
UDP 客户端端口：53472
```