# ICMP 协议是什么？

> 原文：[`c.biancheng.net/view/6399.html`](http://c.biancheng.net/view/6399.html)

控制报文协议（Internet Control Message Protocol，ICMP）是 TCP/IP 协议族的一个子协议。ICMP 协议用于在 IP 主机和路由器之间传递控制消息，描述网络是否通畅、主机是否可达、路由器是否可用等网络状态。

由于 IP 协议简单，数据传输天然存在不可靠、无连接等特点，为了解决数据传输出现的问题，人们引入了 ICMP 协议。虽然 ICMP 协议的数据包并不传输用户数据，但是对于用户数据的传递起着重要的作用。

## ICMP 协议作用

数据包在发送到目标主机的过程中，通常会经过一个或多个路由器。而数据包在通过这些路由进行传输时，可能会遇到各种问题，导致数据包无法发送到目标主机上。为了了解数据包在传输的过程中在哪个环节出现了问题，就需要用到 ICMP 协议。它可以跟踪消息，把问题反馈给源主机。

## ICMP 报文结构

ICMP 报文一般为 8 个字节，包括类型、代码、校验和扩展内容字段。ICMP 报文基本结构如图所示。

![](img/99c4d507b9d9a3cddac52d15f21dc0e7.png)其中，类型表示 ICMP 的消息类型，代码表示对类型的进一步说明，校验和表示对整个报文的报文信息的校验。

在 ICMP 报文中，如果类型和代码不同，ICMP 数据包报告的消息含义也会不同。常见的类型和代码的 ICMP 含义如表所示。

ICMP 类型、代码及含义

| 类型 | 代码 | 含义 |
| 0 | 0 | 回显应答（ping 应答） |
| 3 | 0 | 网络不可达 |
| 3 | 1 | 主机不可达 |
| 3 | 2 | 协议不可达 |
| 3 | 3 | 端口不可达 |
| 3 | 4 | 需要进行分片，但设置不分片位 |
| 3 | 5 | 源站选路失败 |
| 3 | 6 | 目的网络未知 |
| 3 | 7 | 目的主机未知 |
| 3 | 9 | 目的网络被强制禁止 |
| 3 | 10 | 目的主机被强制禁止 |
| 3 | 11 | 由于服务类型 TOS，网络不可达 |
| 3 | 12 | 由于服务类型 TOS，主机不可达 |
| 3 | 13 | 由于过滤，通信被强制禁止 |
| 3 | 14 | 主机越权 |
| 3 | 15 | 优先中止失效 |
| 4 | 0 | 源端被关闭（基本流控制） |
| 5 | 0 | 对网络重定向 |
| 5 | 1 | 对主机重定向 |
| 5 | 2 | 对服务类型和网络重定向 |
| 5 | 3 | 对服务类型和主机重定向 |
| 8 | 0 | 回显请求（ping 请求） |
| 9 | 0 | 路由器通告 |
| 10 | 0 | 路由器请求 |
| 11 | 0 | 传输期间生存时间为 0 |
| 11 | 1 | 在数据报组装期间生存时间为 0 |
| 12 | 0 | 坏的 IP 首部 |
| 12 | 1 | 缺少必需的选项 |
| 13 | 0 | 时间戳请求 |
| 14 | 0 | 时间戳应答 |
| 17 | 0 | 地址掩码请求 |
| 18 | 0 | 地址掩码应答 |