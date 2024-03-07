# Nginx TCP/UDP 代理简述

> 原文：[`www.weixueyuan.net/a/752.html`](http://www.weixueyuan.net/a/752.html)

Nginx 并不直接提供 TCP/UDP 的应用响应，Nginx Stream 模块的核心功能是将客户端的 TCP/UDP 连接反向代理给后端的被代理服务器。

#### 1) 核心配置指令

TCP/UDP 代理功能的核心配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| proxy_bind | address[transparent] 或 off | -- | 设置从指定的本地 IP 地址及端口与被代理服务器建立连接，指令值可以是变量。指令值参数为 transparent 时，允许将客户端的真实 IP 透传给被代理服务器，并以客户端真实 IP 为访问被代理服务器的源 IP；指令值参数为 off 时，则取消上一层指令域同名指令的配置 |
| proxy_buffer_size | size | 16k | 设置用于从被代理服务器读取数据的缓冲区的大小，也用于设置从客户端读取会话数据的缓冲区的大小 |
| proxy_connect_timeout | time | 60s | 与被代理服务器建立连接的超时时间 |
| proxy_timeout | time | 10m | Nginx 服务器与客户端或被代理服务器的两个连续成功的读或写操作的最大间隔时间，如果在间隔时间内没有数据传输，则关闭连接 |
| proxy_download_rate | rate | 0 | 限制每个连接每秒从被代理服务器中读取数据的字节数，默认不限制 |
| proxy_upload_rate | rate | 0 | 限制每个连接每秒发送到被代理服务器的数据的字节数，默认不限制 |
| proxy_next_upstream | on 或 off | on | 当被代理的服务返回错误或超时时，将未返回响应的客户端连接请求传递给 upstream 中的下一个服务器 |
| proxy_next_upstream_timeout | time | 0 | 设置将符合条件的客户端连接请求传递给 upstream 中下一个服务器的超时时间。“0”为不做超时限制，即直到遍历完所有上游服务器组中的服务器为止 |
| proxy_next_upstream_tries | number | 0 | 设置将符合条件的客户端连接请求传递给 upstream 中下一个服务器的尝试次数，包括第一次的失败次数。“0”为不做尝试次数限制，即直到遍历完所有上游服务器组中的服务器为止 |
| proxy_pass | address | -- | 被代理服务器的地址，支持 IP 或域名加端口、UNIX 域套接字、upstream 名 |
| proxy_protocol | on 或 off | off | 设置是否对被代理服务器的连接启用代理协议（proxy_protocol）支持 |
| proxy_socket_keepalive | on 或 off | off | 设置 Nginx 与被代理服务器的 TCP keepalive 行为的心跳检测机制，默认使用操作系统的 socket 配置，若指令值为 on，则开启 SO_KEEPALIVE 选项进行心跳检测 |
| proxy_ssl | on 或 off | off | 设置是否启用 SSL/TLS 协议与被代理服务器建立连接 |
| proxy_ssl_protocols | [SSLv2][SSLv3] [TLSv1][TLSv1.1]
[TLSv1.2][TLSv1.3] | TLSv1 TLSv1.1
TLSv1.2 | 指定可用于 Nginx 与被代理服务器建立 SSL 连接的 SSL 协议版本 |
| proxy_ssl_session_reuse | on 或 off | on | 是否启用与被代理服务器 SSL TCP 连接的 SSL 会话重用功能 |
| proxy_ssl_ciphers | ciphers | DEFAULT | 设置与被代理服务器建立 SSL 连接时，用于协商使用的加密算法组合，也称为密码套件，指令值内容为 openssl 的密码套件名称，多个套件名称由“:”分隔 |
| proxy_ssl_server_name | on 或 off | off | 在与被代理服务器建立 SSL 连接时，设置是否启用通过 SNI 或 RFC 6066 传递主机名 |
| proxy_ssl_certificate | file | -- | 指定被代理服务器对 Nginx 服务器身份验证的 PEM 格式 SSL 证书文件 |
| proxy_ssl_certificate_key | file | -- | 指定被代理服务器对 Nginx 服务器身份验证的 PEM 格式 SSL 证书私钥文件 |
| proxy_ssl_password_file | file | -- | 存放被代理服务器对 Nginx 服务器身份验证的 PEM 格式 SSL 证书私钥文件的密码文件，一个密码一行。有多个密码时，Nginx 会依次尝试 |
| proxy_ssl_verify | on 或 off | off | 设置是否启用对被代理服务器的 SSL 证书的验证功能 |
| proxy_ssl_name | name | proxy_pass 指令指定的主机名 | 指定对被代理服务器 SSL 证书验证的主机名 |
| proxy_ssl_crl | file | -- | 证书品销列表文件，用于验证被代理服务器 SSL 证书有效性的 PEM 格式文件 |
| proxy_ssl_trusted_certificate | file | -- | 指定一个 PEM 格式的 CA 证书（根或中间证书）文件，该证书用作被代理服务器的证书链验证 |
| proxy_ssl_verify_depth | number | 1 | 设置被代理服务器的证书链的验证深度 |
| proxy_requests | number | 0 | UDP 代理时，设置同一客户端被 Nginx 在每次 UDP 会话中，转发给被代理服务器的数据报的数量。当达到这个数量时，将启用一个新的 UDP 会话继续转发。可用于 Nginx 对 UDP 虚拟连接会话的控制 |
| proxy_responses | number | -- | UDP 代理时，设置允许被代理服务器返回 UDP 数据报的数量，超过指令值时将中止会话。默认无限制，0 为不返回响应数据 |

该模块的指令使用的指令域范围为 stream、server。

#### 2) TCP 反向代理配置样例

配置样例如下：

```

stream {
    server {
        listen 389 ;                          # 设置监听端口为 389
        proxy_pass 192.168.2.100:389;         # 将连接代理到后端 192.168.2.100:389
        proxy_timeout 5s;                     # 与被代理服务器的连续通信间隔大于 5s，
                                              # 则认为通信超时，将关闭连接
        proxy_connect_timeout 5s;             # 与被代理服务器建立连接的超时时间为 5s
        access_log logs/ldap_access.log tcp;  # 记录日志文件为 logs/ldap_access.log，
                                              # 日志模板为 tcp
    }
}
```

#### 3) 代理 SSL TCP

代理模块 stream 可以实现基于 SSL/TLS 协议的被代理服务器的反向代理，部署方式为客户端 → Nginx 服务器（TCP）→ 被代理服务器（SSL TCP）。配置样例如下：

```

stream {
    server{
        listen 636;                            # 设置监听端口为 636
        access_log  logs/ldap_access.log tcp;
        proxy_pass  192.168.2.100:636;
        proxy_ssl   on;                        # 启用 SSL/TLS 协议，与被代理服务器建立连接
        proxy_ssl_session_reuse on;            # 与被代理服务器 SSL TCP 连接的 SSL 会话重用功能
    }
}
```

#### 4) UDP 反向代理配置

UDP 协议是一种无连接的协议，发送端与接收端传输数据之前不需要建立连接，发送端会尽最大努力把数据发送出去，不能保证安全地传输到接收端。由于传输数据不建立连接，也不需要维持复杂的链路关系（包括连接状态、收发状态等），因此发送端可同时向多个接收端传输相同的消息。

虽然 UDP 的数据传输是不可靠的，但如果有一个数据报丢失，另一个新的数据报会在几秒内替换它发送到接收端。UDP 协议通常被用在单向传输无须返回响应及信息分发的场景，如日志收集或在屏幕上的航班信息、股票行情等多媒体场景。

```

stream {
    server {
        listen 1514 udp;               # 设置监听端口为 1514 并启用 UDP 协议
        proxy_pass 192.168.2.123:1514;
        proxy_responses 0;             # 会话接收数据报后无须等待返回响应，立即关闭会话
    }
}
```