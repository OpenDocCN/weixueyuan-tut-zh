# 基于 UDP 的服务器端和客户端

前面的文章中我们给出了几个 TCP 的例子，对于 UDP 而言，只要能理解前面的内容，实现并非难事。

## UDP 中的服务器端和客户端没有连接

UDP 不像 TCP，无需在连接状态下交换数据，因此基于 UDP 的服务器端和客户端也无需经过连接过程。也就是说，不必调用 listen() 和 accept() 函数。UDP 中只有创建套接字的过程和数据交换的过程。

## UDP 服务器端和客户端均只需 1 个套接字

TCP 中，套接字是一对一的关系。如要向 10 个客户端提供服务，那么除了负责监听的套接字外，还需要创建 10 套接字。但在 UDP 中，不管是服务器端还是客户端都只需要 1 个套接字。之前解释 UDP 原理的时候举了邮寄包裹的例子，负责邮寄包裹的快递公司可以比喻为 UDP 套接字，只要有 1 个快递公司，就可以通过它向任意地址邮寄包裹。同样，只需 1 个 UDP 套接字就可以向任意主机传送数据。

## 基于 UDP 的接收和发送函数

创建好 TCP 套接字后，传输数据时无需再添加地址信息，因为 TCP 套接字将保持与对方套接字的连接。换言之，TCP 套接字知道目标地址信息。但 UDP 套接字不会保持连接状态，每次传输数据都要添加目标地址信息，这相当于在邮寄包裹前填写收件人地址。

发送数据使用 sendto() 函数：

```
ssize_t sendto(int sock, void *buf, size_t nbytes, int flags, struct sockaddr *to, socklen_t addrlen);  //Linux
int sendto(SOCKET sock, const char *buf, int nbytes, int flags, const struct sockadr *to, int addrlen);  //Windows
```

Linux 和 Windows 下的 sendto() 函数类似，下面是详细参数说明：

*   sock：用于传输 UDP 数据的套接字；
*   buf：保存待传输数据的缓冲区地址；
*   nbytes：带传输数据的长度（以字节计）；
*   flags：可选项参数，若没有可传递 0；
*   to：存有目标地址信息的 sockaddr 结构体变量的地址；
*   addrlen：传递给参数 to 的地址值结构体变量的长度。

UDP 发送函数 sendto() 与 TCP 发送函数 write()/send() 的最大区别在于，sendto() 函数需要向他传递目标地址信息。

接收数据使用 recvfrom() 函数：

```
ssize_t recvfrom(int sock, void *buf, size_t nbytes, int flags, struct sockadr *from, socklen_t *addrlen);  //Linux
int recvfrom(SOCKET sock, char *buf, int nbytes, int flags, const struct sockaddr *from, int *addrlen);  //Windows
```

由于 UDP 数据的发送端不不定，所以 recvfrom() 函数定义为可接收发送端信息的形式，具体参数如下：

*   sock：用于接收 UDP 数据的套接字；
*   buf：保存接收数据的缓冲区地址；
*   nbytes：可接收的最大字节数（不能超过 buf 缓冲区的大小）；
*   flags：可选项参数，若没有可传递 0；
*   from：存有发送端地址信息的 sockaddr 结构体变量的地址；
*   addrlen：保存参数 from 的结构体变量长度的变量地址值。

## 基于 UDP 的回声服务器端/客户端

下面结合之前的内容实现回声客户端。需要注意的是，UDP 不同于 TCP，不存在请求连接和受理过程，因此在某种意义上无法明确区分服务器端和客户端，只是因为其提供服务而称为服务器端，希望各位读者不要误解。

下面给出 Windows 下的代码，Linux 与此类似，不再赘述。

服务器端 server.cpp：

```
#include <stdio.h>
#include <winsock2.h>
#pragma comment (lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main(){
    WSADATA wsaData;
    WSAStartup( MAKEWORD(2, 2), &wsaData);

    //创建套接字
    SOCKET sock = socket(AF_INET, SOCK_DGRAM, 0);

    //绑定套接字
    sockaddr_in servAddr;
    memset(&servAddr, 0, sizeof(servAddr));  //每个字节都用 0 填充
    servAddr.sin_family = PF_INET;  //使用 IPv4 地址
    servAddr.sin_addr.s_addr = htonl(INADDR_ANY); //自动获取 IP 地址
    servAddr.sin_port = htons(1234);  //端口
    bind(sock, (SOCKADDR*)&servAddr, sizeof(SOCKADDR));

    //接收客户端请求
    SOCKADDR clntAddr;  //客户端地址信息
    int nSize = sizeof(SOCKADDR);
    char buffer[BUF_SIZE];  //缓冲区
    while(1){
        int strLen = recvfrom(sock, buffer, BUF_SIZE, 0, &clntAddr, &nSize);
        sendto(sock, buffer, strLen, 0, &clntAddr, nSize);
    }

    closesocket(sock);
    WSACleanup();
    return 0;
}
```

代码说明：
1) 第 12 行代码在创建套接字时，向 socket() 第二个参数传递 SOCK_DGRAM，以指明使用 UDP 协议。

2) 第 18 行代码中使用`htonl(INADDR_ANY)`来自动获取 IP 地址。

利用常数 INADDR_ANY 自动获取 IP 地址有一个明显的好处，就是当软件安装到其他服务器或者服务器 IP 地址改变时，不用再更改源码重新编译，也不用在启动软件时手动输入。而且，如果一台计算机中已分配多个 IP 地址（例如路由器），那么只要端口号一致，就可以从不同的 IP 地址接收数据。所以，服务器中优先考虑使用 INADDR_ANY；而客户端中除非带有一部分服务器功能，否则不会采用。

客户端 client.cpp：

```
#include <stdio.h>
#include <WinSock2.h>
#pragma comment(lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main(){
    //初始化 DLL
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);

    //创建套接字
    SOCKET sock = socket(PF_INET, SOCK_DGRAM, 0);

    //服务器地址信息
    sockaddr_in servAddr;
    memset(&servAddr, 0, sizeof(servAddr));  //每个字节都用 0 填充
    servAddr.sin_family = PF_INET;
    servAddr.sin_addr.s_addr = inet_addr("127.0.0.1");
    servAddr.sin_port = htons(1234);

    //不断获取用户输入并发送给服务器，然后接受服务器数据
    sockaddr fromAddr;
    int addrLen = sizeof(fromAddr);
    while(1){
        char buffer[BUF_SIZE] = {0};
        printf("Input a string: ");
        gets(buffer);
        sendto(sock, buffer, strlen(buffer), 0, (struct sockaddr*)&servAddr, sizeof(servAddr));
        int strLen = recvfrom(sock, buffer, BUF_SIZE, 0, &fromAddr, &addrLen);
        buffer[strLen] = 0;
        printf("Message form server: %s\n", buffer);
    }

    closesocket(sock);
    WSACleanup();
    return 0;
}
```

先运行 server，再运行 client，client 输出结果为：

Input a string: C 语言中文网
Message form server: C 语言中文网
Input a string: c.biancheng.net Founded in 2012
Message form server: c.biancheng.net Founded in 2012
Input a string:

从代码中可以看出，server.cpp 中没有使用 listen() 函数，client.cpp 中也没有使用 connect() 函数，因为 UDP 不需要连接。