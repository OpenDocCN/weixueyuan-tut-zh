# Nginx 负载均衡模块简述

> 原文：[`www.weixueyuan.net/a/778.html`](http://www.weixueyuan.net/a/778.html)

Nginx 负载均衡是由代理模块和上游（upstream）模块共同实现的，Nginx 通过代理模块的反向代理功能将用户请求转发到上游服务器组，上游模块通过指定的负载均衡策略及相关的参数配置将用户请求转发到目标服务器上。上游模块可以与 Nginx 的代理指令（proxy_pass）、FastCGI 协议指令（fastcgi_pass）、uWSGI 协议指令（uwsgi_pass）、SCGI 协议指令（scgi_pass）、memcached 指令（memcached_pass）及 gRPC 协议指令（grpc_pass）实现多种协议后端服务器的负载均衡。

## 1、服务器配置指令

Nginx 上游模块定义了 upstream 指令域，在该指令域内可设置服务器、负载均衡策略等负载均衡配置，配置样例如下，具体指令说明如下面表格中所示。

```

upstream backend {
    server backend1.example.com       weight=5;     # 被代理服务器端口号为 80，权重为 5
    server backend2.example.com:8080;               # 被代理服务器端口号为 8080，默认权重为 1
    server unix:/tmp/backend3;

    server backup1.example.com:8080   backup;       # 该被代理服务器为备份状态
    server backup2.example.com:8080   backup;       # 该被代理服务器为备份状态
}

server {
    location / {
        proxy_pass http://backend;          # 将客户端请求反向代理到上游服务器组 backend
    }
}
```

表：服务器指令

| 名称 | 服务器指令 |
| 指令 | server |
| 作用域 | upstream |
| 配置格式 | address[parameters]; |
| 指令说明 | 设定上游服务器组的服务器地址及连接参数 |

关于上表有以下几点需要注意：

*   服务器地址可以是指定端口的 IP、域名或 Unix 套接字；
*   如不指定端口，默认端口号为 80。

表：服务器指令参数

| 参数 | 参数名称 | 参数类型 | 默认值 | 参数说明 |
| weight | 权重 | int | 1 | 设置服务器的权重 |
| max_fails | 最大失败数 | int | 1 | 被代理服务器在 fail timeout 规定时间内的最大请求失败次数，超过设定值后，被代理服务器便被认为不可用。是否失败由 proxy_next_upstream、fastcgi_next_upstream、uwsgi_next_upstream、scgi_next_upstream、memcached_next_upstream 及 grpe_next_upstream 指令定义。0 表示关闭被代理服务器的失败检测功能 |
| fail_timeout | 失败超时 | time | 10s | 被代理服务器被置为不可用的最长时间及被代理服务器被连续失败检测的最长时间 |
| backup | 备份服务器 | -- | -- | 将被代理服务器标为备份状态，当其他非备份被代理服务器不可用时，会把请求转发给备份被代理服务器 |
| down | 无效服务器 | -- | -- | 将被代理服务器标为不可用状态 |
| max_conns | 最大连接数 | int | 0 | 与被代理服务器建立活动连接的最大数量，默认值 0 表示没有限制 |
| resolve | 动态解析 | -- | -- | 在被代理服务器域名对应的 IP 变化时，自动更新被代理服务器的 IP，该参数依赖 resolver 指令设置的域名解析服务器。仅对商业版本有效 |
| service | DNS SRV 记录 | name | -- | DNS SRV 记录设置。仅对商业版本有效 |
| slow_start | 慢恢复时间 | time | 0 | 不可用服务器在设置时间内检测持续有效后便被恢复正常，默认关闭。仅对商业版本有效 |

其中，slow_start 参数不能与 Hash 负载均衡方法一同使用；若上游服务器组中只有一台被代理服务器，则 max_fails、fail_timeout 和 slow_start 参数都会被忽略，并且这个服务器将永远不会被置为无效。

表：共享内存区指令

| 名称 | 共享内存区指令 |
| 指令 | zone |
| 作用域 | upstream |
| 配置格式 | name[size]; |
| 指令说明 | 设定共享内存区域的名称及大小，用以在多个工作进程间共享配置及运行时的状态 |

表：长连接最大请求数指令

| 名称 | 长连接最大请求数指令 |
| 指令 | keepalive_requests |
| 作用域 | upstream |
| 指令格式 | number; |
| 默认值 | 100; |
| 指令说明 | 设置每个与被代理服务器建立的长连接中传输请求的最大数量，超过这个值后，该连接将被关闭 |

表：长连接缓存数

| 名称 | 长连接缓存数 |
| 指令 | keepalive |
| 作用域 | upstream |
| 指令格式 | connections; |
| 指令说明 | 当 Nginx 与被代理服务器建立长连接时，设定每个工作进程可以缓存的与当前上游服务器组中被代理服务器保持长连接的数量。当超过设定值时，将根据最近最少使用算法（LRU）关闭连接 |

注意：该指令不会对活跃的 TCP 连接数有影响。

表：长连接缓存超时时间

| 名称 | 长连接缓存超时时间 |
| 指令 | keepalive_timeout |
| 作用域 | upstream |
| 指令格式 | timeout; |
| 默认值 | 60; |
| 指令说明 | 设置长连接缓存中，每个连接的超时时间，被缓存的连接超过这个时间仍未被激活使用时将被关闭 |

## 2、负载均衡策略指令

Nginx 支持多种负载均衡策略，如轮询（Round Robin）、一致性哈希（Consistent Hash）、IP 哈希（IP Hash）、最少连接（least_conn）等。Nginx 的默认负载均衡策略为轮询策略，不需要配置指令，轮询策略通过 server 的权重参数可实现手动分配的加权轮询策略。

负载均衡策略配置指令均应编辑在 upstream 指令域的最上方，常见的配置指令如下面表格中所示。

表：哈希策略

| 名称 | 哈希策略 |
| 指令 | hash |
| 作用域 | upstream |
| 指令格式 | key[consistent]; |
| 默认值 | -- |
| 指令说明 | 设置用于哈希策略计算哈希值的键值，并对上游服务器组启用哈希的负载均衡策略。键值可以是文本、变量及其组合，当 consistent 参数被指定时，将启用 Ketama 一致性哈希的负载均衡策略 |

配置样例如下：

```

upstream backend {
    hash $request_uri;              # 以客户端请求 URI 为计算哈希值的 key
    ...
}

upstream backend {
    hash $request_uri consistent;   # 以客户端请求 URI 为计算哈希值的 key，采用一致性哈希算法
    ...
}
```

表：IP 哈希策略

| 名称 | IP 哈希策略 |
| 指令 | ip_hash |
| 作用域 | upstream |
| 默认值 | -- |
| 指令说明 | 设置启用 IP 哈希负载均衡策略，根据客户端的 IPv4 地址的前三个八位字节或整个 IPv6 地址作为哈希键计算哈希值，该方法确保同一客户端的请求总会被同一被代理服务器处理。当 IP 哈希值对应的被代理服务器不可用时，请求将被分配给其他服务器 |

配置样例如下：

```

upstream backend {
    ip_hash;                # 启用 IP 哈希负载均衡策略
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com down;
    server backend4.example.com;
}
```

当服务器组中一台服务器被临时删除时，可使用 down 参数标记，那么客户端 IP 哈希值将会保留。

表：最少连接策略

| 名称  | 最少连接策略 |
| 指令 | least_conn |
| 作用域 | upstream |
| 默认值  | -- |
| 指令说明 | 在考虑上游服务器组中各服务器权重的前提下，将客户端请求分配给拥有最少活跃连接被代理服务器的负载均衡策略 |

配置样例如下：

```

upstream backend {
    least_conn;             # 启用最少连接负载均衡策略
    server backend1.example.com;
    server backend2.example.com;
    server backend4.example.com;
}
```

表：随机负载策略

| 名称 | 随机负载策略 |
| 指令 | random |
| 作用域 | upstream |
| 默认值 | -- |
| 指令说明 | 在考虑上游服务器组中各服务器权重的前提下，将客户端请求分配给随机选择的被代理服务器 |

配置样例如下：

```

upstream backend {
    random;                 # 每个请求都被随机发送到某个服务器
    server backend1.example.com;
    server backend2.example.com;
    server backend4.example.com;
}
```

指令值参数 two，该参数表示随机选择两台被代理服务器，然后使用指定的负载策略进行选择，默认方法为 least_conn；可被指定的负载策略为 least_conn、least_time（仅对商业版有效）。