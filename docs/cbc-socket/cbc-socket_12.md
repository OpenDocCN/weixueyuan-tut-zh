# TCP 的粘包问题以及数据的无边界性

上节我们讲到了 socket 缓冲区和数据的传递过程，可以看到数据的接收和发送是无关的，read()/recv() 函数不管数据发送了多少次，都会尽可能多的接收数据。也就是说，read()/recv() 和 write()/send() 的执行次数可能不同。

例如，write()/send() 重复执行三次，每次都发送字符串"abc"，那么目标机器上的 read()/recv() 可能分三次接收，每次都接收"abc"；也可能分两次接收，第一次接收"abcab"，第二次接收"cabc"；也可能一次就接收到字符串"abcabcabc"。

假设我们希望客户端每次发送一位学生的学号，让服务器端返回该学生的姓名、住址、成绩等信息，这时候可能就会出现问题，服务器端不能区分学生的学号。例如第一次发送 1，第二次发送 3，服务器可能当成 13 来处理，返回的信息显然是错误的。

这就是数据的“粘包”问题，客户端发送的多个数据包被当做一个数据包接收。也称数据的无边界性，read()/recv() 函数不知道数据包的开始或结束标志（实际上也没有任何开始或结束标志），只把它们当做连续的数据流来处理。

下面的代码演示了粘包问题，客户端连续三次向服务器端发送数据，服务器端却一次性接收到所有数据。

服务器端代码 server.cpp：

```
#include <stdio.h>
#include <windows.h>
#pragma comment (lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main(){
    WSADATA wsaData;
    WSAStartup( MAKEWORD(2, 2), &wsaData);

    //创建套接字
    SOCKET servSock = socket(AF_INET, SOCK_STREAM, 0);

    //绑定套接字
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用 0 填充
    sockAddr.sin_family = PF_INET;  //使用 IPv4 地址
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的 IP 地址
    sockAddr.sin_port = htons(1234);  //端口
    bind(servSock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));

    //进入监听状态
    listen(servSock, 20);

    //接收客户端请求
    SOCKADDR clntAddr;
    int nSize = sizeof(SOCKADDR);
    char buffer[BUF_SIZE] = {0};  //缓冲区
    SOCKET clntSock = accept(servSock, (SOCKADDR*)&clntAddr, &nSize);

    Sleep(10000);  //注意这里，让程序暂停 10 秒

    //接收客户端发来的数据，并原样返回
    int recvLen = recv(clntSock, buffer, BUF_SIZE, 0);
    send(clntSock, buffer, recvLen, 0);

    //关闭套接字并终止 DLL 的使用
    closesocket(clntSock);
    closesocket(servSock);
    WSACleanup();

    return 0;
}
```

客户端代码 client.cpp：

```
#include <stdio.h>
#include <stdlib.h>
#include <WinSock2.h>
#include <windows.h>
#pragma comment(lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main(){
    //初始化 DLL
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);

    //向服务器发起请求
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用 0 填充
    sockAddr.sin_family = PF_INET;
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");
    sockAddr.sin_port = htons(1234);

    //创建套接字
    SOCKET sock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);
    connect(sock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));

    //获取用户输入的字符串并发送给服务器
    char bufSend[BUF_SIZE] = {0};
    printf("Input a string: ");
    gets(bufSend);
    for(int i=0; i<3; i++){
        send(sock, bufSend, strlen(bufSend), 0);
    }
    //接收服务器传回的数据
    char bufRecv[BUF_SIZE] = {0};
    recv(sock, bufRecv, BUF_SIZE, 0);
    //输出接收到的数据
    printf("Message form server: %s\n", bufRecv);

    closesocket(sock);  //关闭套接字
    WSACleanup();  //终止使用 DLL

    system("pause");
    return 0;
}
```

先运行 server，再运行 client，并在 10 秒内输入字符串"abc"，再等数秒，服务器就会返回数据。运行结果如下：
Input a string: abc
Message form server: abcabcabc

本程序的关键是 server.cpp 第 31 行的代码`Sleep(10000);`，它让程序暂停执行 10 秒。在这段时间内，client 连续三次发送字符串"abc"，由于 server 被阻塞，数据只能堆积在缓冲区中，10 秒后，server 开始运行，从缓冲区中一次性读出所有积压的数据，并返回给客户端。

另外还需要说明的是 client.cpp 第 34 行代码。client 执行到 recv() 函数，由于输入缓冲区中没有数据，所以会被阻塞，直到 10 秒后 server 传回数据才开始执行。用户看到的直观效果就是，client 暂停一段时间才输出 server 返回的结果。

client 的 send() 发送了三个数据包，而 server 的 recv() 却只接收到一个数据包，这很好的说明了数据的粘包问题。