# Nginx 端口监听（listen 指令）

> 原文：[`www.weixueyuan.net/a/635.html`](http://www.weixueyuan.net/a/635.html)

本节主要来介绍 Nginx 中与端口监听有关的配置指令，下表为端口监听指令及其相关说明。

| 名称 | 端口监听指令 |
| 指令 | listen |
| 作用域 | server |
| 默认值 | listen*:80 或 *:8000 |
| 指令说明 | 服务监听端口、绑定 IP、监听方式的配置 |

Nginx 服务通过 listen 指令的指令值监听网络请求，可以是 IP 协议的形式，也可以是 UNIX 域套接字。如果不设置 listen 指令，Nginx 在以超级用户运行时则监听 80 端口，以非超级用户运行时则监听 8000 端口。

listen 指令的指令值还针对监听方式提供了丰富的参数，如下表所示。

| 参数 | 默认值  | 参数说明 |
| address | -- | 若为 IP 协议，该参数值为指定绑定监听端口的 IP 或主机名；若为 UNIX 域套接字，则该参数值为 sock 文件路径 |
| port | 80 | IP 协议监听的端口 |
| bind | address:port | 指定 IP 及端口 |
| ipv6only | on | 只接收 IPv6 连接或接收 IPv6 和 IPv4 连接 |
| default_server | -- | 当 http 指令域中包含多个虚拟主机时，该参数用于指定哪个虚拟主机是默认服务，默认将第一个顺序的 server 设为默认服务。默认服务可以用来处理没有 server_name 匹配成功的请求 |
| http2 | -- | HTTP/2 协议支持 |
| spdy | -- | SDPY 协议支持，与 HTTP/2 协议不能同时存在 |
| ssl | -- | SSL 支持 |
| proxy_protocol | -- | 在指定监听端口上启用 proxy_protocol 协议支持 |
| fastopen | number | HTTP 处于保持连接（keepalive）状态时，允许不经过三次握手的 TCP 连接的队列的最大数 |
| deferred | -- | 添加该参数后，在 TCP 三次握手的过程中，检测到客户端有数据时才将 TCP 状态置为 ESTABLISHED 状态，没有数据则直接丢弃 |
| reuseport | -- | 默认情况下，所有的工作进程会共享一个 socket 去监听同一 IP 和端口的组合。该参数启用后，允许每个工作进程有独立的 socket 去监听同一 IP 和端口的组合，内核会对传人的连接进行负载均衡。适用于 Linux 3.9+，DragonFly BSD 和 FreeBSD 12+ |
| so_keepalive | off | 配置是否在监听的端口启用“TCP keepalive”机制。当设置为 on 时，默认等同于 so_keepalive=30m::10，表示 30 分钟无数据传输时发送探测包，发送 10 次，发送间隔使用系统内核参数 tcp_keepalive_intvl 的设定值 |
| backlog | -1/511 | 当阻塞时，设置挂起连接队列的最大长度，在 FreeBSD，DragonFly BSD 和 MacOS 操作系统上，默认值为 -1，其他平台上值为 511 |
| rcvbuf | -- | socket 接收缓冲的大小，默认为 8k 字节，在接收数据比较大的场景中可以适当调整 |
| sndbuf | -- | socket 发送缓冲的大小，默认为 8k 字节，在发送数据较大的场景中可以适当调整 |
| setfib | number | 为监听套接字设置关联路由表，仅在 FreeBSD 系统上有效 |
| accept_filter | filter | 为监听套接字设置过滤器，仅支持 FreeBSD 和 NetBSD 5.0+ 系统 |

配置样例如下：

http {
    server {
        listen 127.0.0.1:8000;           # 监听 127.0.0.1 的 8000 端口
        listen 127.0.0.1;                    # 监听 127.0.0.1 的默认 80 端口（root 权限）
        listen 8000;                          # 监听本机所有 IP 的 8000 端口
        listen *:8000;                        # 监听本机所有 IP 的 8000 端口
        listen localhost:8000;           # 监听 locahost 的 8000 端口
        listen [::]:8000;                      # 监听 IPv6 的 8000 端口
        listen [::1];                             # 监听 IPv6 的回环 IP 的默认 80 端口(root 权限)
        listen unix:/var/run/nginx.sock; # 监听域套接字文件

        listen *:8000 \                     # 监听本机的 8000 端口
                default_server \          # 当前服务是 http 指令域的主服务
                fastopen=30 \            # 开启 fastopen 功能并限定最大队列数为 30
                deferred \                   # 拒绝空数据连接
                reuseport \                 # 工作进程共享 socket 这个监听端口
                backlog=1024 \          # 请求阻塞时挂起队列数是 1024 个
                so_keepalive=on;        # 当 socket 为保持连接时，开启状态检测功能

    }
}