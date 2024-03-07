# WSAStartup()函数以及 DLL 的加载

> 本节讲解 Windows 下 DLL 的加载，学习 Linux Socket 的读者可以跳过。

WinSock（Windows Socket）编程依赖于系统提供的动态链接库(DLL)，有两个版本：

*   较早的 DLL 是 wsock32.dll，大小为 28KB，对应的头文件为 winsock1.h；
*   最新的 DLL 是 ws2_32.dll，大小为 69KB，对应的头文件为 winsock2.h。

几乎所有的 Windows 操作系统都已经支持 ws2_32.dll，包括个人操作系统 Windows 95 OSR2、Windows 98、Windows Me、Windows 2000、XP、Vista、Win7、Win8、Win10 以及服务器操作系统 Windows NT 4.0 SP4、Windows Server 2003、Windows Server 2008 等，所以你可以毫不犹豫地使用最新的 ws2_32.dll。

使用 DLL 之前必须把 DLL 加载到当前程序，你可以在编译时加载，也可以在程序运行时加载，《[C 语言高级教程](http://c.biancheng.net/cpp/u/gaoji/)》中讲到了这两种加载方式，请猛击：[动态链接库 DLL 的加载：隐式加载(载入时加载)和显式加载(运行时加载)](http://c.biancheng.net/cpp/html/2754.html)。

这里使用`#pragma`命令，在编译时加载：

```
#pragma comment (lib, "ws2_32.lib")
```

## WSAStartup() 函数

使用 DLL 之前，还需要调用 WSAStartup() 函数进行初始化，以指明 WinSock 规范的版本，它的原型为：

```
int WSAStartup(WORD wVersionRequested, LPWSADATA lpWSAData);
```

wVersionRequested 为 WinSock 规范的版本号，低字节为主版本号，高字节为副版本号（修正版本号）；lpWSAData 为指向 WSAData 结构体的指针。

#### 关于 WinSock 规范

WinSock 规范的最新版本号为 2.2，较早的有 2.1、2.0、1.1、1.0，ws2_32.dll 支持所有的规范，而 wsock32.dll 仅支持 1.0 和 1.1。

wsock32.dll 已经能够很好的支持 TCP/IP 通信程序的开发，ws2_32.dll 主要增加了对其他协议的支持，不过建议使用最新的 2.2 版本。

wVersionRequested 参数用来指明我们希望使用的版本号，它的类型为 WORD，等价于 unsigned short，是一个整数，所以需要用 MAKEWORD() 宏函数对版本号进行转换。例如：

```
MAKEWORD(1, 2);  //主版本号为 1，副版本号为 2，返回 0x0201
MAKEWORD(2, 2);  //主版本号为 2，副版本号为 2，返回 0x0202
```

#### 关于 WSAData 结构体

WSAStartup() 函数执行成功后，会将与 ws2_32.dll 有关的信息写入 WSAData 结构体变量。WSAData 的定义如下：

```
typedef struct WSAData {
    WORD           wVersion;  //ws2_32.dll 建议我们使用的版本号
    WORD           wHighVersion;  //ws2_32.dll 支持的最高版本号
    //一个以 null 结尾的字符串，用来说明 ws2_32.dll 的实现以及厂商信息
    char           szDescription[WSADESCRIPTION_LEN+1];
    //一个以 null 结尾的字符串，用来说明 ws2_32.dll 的状态以及配置信息
    char           szSystemStatus[WSASYS_STATUS_LEN+1];
    unsigned short iMaxSockets;  //2.0 以后不再使用
    unsigned short iMaxUdpDg;  //2.0 以后不再使用
    char FAR       *lpVendorInfo;  //2.0 以后不再使用
} WSADATA, *LPWSADATA;
```

最后 3 个成员已弃之不用，szDescription 和 szSystemStatus 包含的信息基本没有实用价值，读者只需关注前两个成员即可。请看下面的代码：

```
#include <stdio.h>
#include <winsock2.h>
#pragma comment (lib, "ws2_32.lib")

int main(){
    WSADATA wsaData;
    WSAStartup( MAKEWORD(2, 2), &wsaData);

    printf("wVersion: %d.%d\n", LOBYTE(wsaData.wVersion), HIBYTE(wsaData.wVersion));
    printf("wHighVersion: %d.%d\n", LOBYTE(wsaData.wHighVersion), HIBYTE(wsaData.wHighVersion));
    printf("szDescription: %s\n", wsaData.szDescription);
    printf("szSystemStatus: %s\n", wsaData.szSystemStatus);

    return 0;
}
```

运行结果：
wVersion: 2.2
wHighVersion: 2.2
szDescription: WinSock 2.0
szSystemStatus: Running

ws2_32.dll 支持的最高版本为 2.2，建议使用的版本也是 2.2。

综上所述：WinSock 编程的第一步就是加载 ws2_32.dll，然后调用 WSAStartup() 函数进行初始化，并指明要使用的版本号。