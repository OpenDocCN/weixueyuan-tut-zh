# Nginx Stream(TCP/UDP)负载均衡

> 原文：[`www.weixueyuan.net/a/790.html`](http://www.weixueyuan.net/a/790.html)

Nginx 的 TCP/UDP 负载均衡是应用 Stream 代理模块（ngx_stream_proxy_module）和 Stream 上游模块（ngx_stream_upstream_module）实现的。Nginx 的 TCP 负载均衡与 LVS 都是四层负载均衡的应用，所不同的是，LVS 是被置于 Linux 内核中的，而 Nginx 是运行于用户层的，基于 Nginx 的 TCP 负载可以实现更灵活的用户访问管理和控制。

## 1、TCP/UDP 负载均衡

Nginx 的 Stream 上游模块支持与 Nginx HTTP 上游模块一致的轮询（Round Robin）、哈希（Hash）及最少连接数（least_conn）负载均衡策略。Nginx 默认使用轮询负载均衡策略，配置样例如下：

```

stream {
    upstream backend {
        server 192.168.2.145:389 weight=5;
        server 192.168.2.159:389 weight=1;
        server 192.168.2.109:389 weight=1;
    }

    server {
        listen 389;
        proxy_pass backend;
    }
}
```

哈希负载均衡策略可以通过客户端 IP（$remote_addr）实现简单的会话保持，其可将同一 IP 客户端始终转发给同一台后端服务器。

配置样例如下：

```

stream {
    upstream backend {
        hash $remote_addr;
        server 192.168.2.145:389 weight=5;
        server 192.168.2.159:389 weight=1;
        server 192.168.2.109:389 weight=1;
    }

    server {
        listen 389;
        proxy_pass backend;
    }
}
```

哈希负载均衡策略通过指令参数 consistent 设定是否开启一致性哈希负载均衡策略。Nginx 的一致性哈希负载均衡策略是采用 Ketama 一致性哈希算法，当后端服务器组中的服务器数量变化时，只会影响少部分客户端的请求。

配置样例如下：

```

stream {
    upstream backend {
        hash $remote_addr consistent;
        server 192.168.2.145:389 weight=5;
        server 192.168.2.159:389 weight=1;
        server 192.168.2.109:389 weight=1;
    }

    server {
        listen 389;
        proxy_pass backend;
    }
}
```

最少连接负载均衡策略，可以在后端被代理服务器性能不均时，在考虑上游服务器组中各服务器权重的前提下，将客户端连接分配给活跃连接最少的被代理服务器，从而有效提高处理性能高的被代理服务器的使用率。

配置样例如下：

```

stream {
    upstream backend {
        least_conn;
        server 192.168.2.145:389 weight=5;
        server 192.168.2.159:389 weight=1;
        server 192.168.2.109:389 weight=1;
    }

    server {
        listen 389;
        proxy_pass backend;
    }
}
```

## 2、TCP/UDP 负载均衡的容错机制

Nginx 的 TCP/UDP 负载均衡在连接分配时也支持被动健康检测模式，如果与后端服务器建立连接失败，并在 fail_timeout 参数的时间内连续超过 max_fails 参数设置的次数，Nginx 就会将该服务器置为不可用状态，并且在 fail_timeout 参数的时间内不再给该服务器分配连接。当 fail_timeout 参数的时间结束时将尝试分配连接检测该服务器是否恢复，如果可以建立连接，则判定为恢复。

配置样例如下：

```

stream {
    upstream backend {
        # 10s 内出现 3 次错误，该服务器将被熔断 10s
        server 192.168.2.154:8080 max_fails=3 fail_timeout=10s;
        server 192.168.2.109:8080 max_fails=3 fail_timeout=10s;
        server 192.168.2.108:8080 max_fails=3 fail_timeout=10s;
        server 192.168.2.107:8080 max_fails=3 fail_timeout=10s;
    }

    server {
        proxy_connect_timeout 5s;           # 与被代理服务器建立连接的超时时间为 5s
        proxy_timeout 10s;          # 获取被代理服务器的响应最大超时时间为 10s

        # 当被代理的服务器返回错误或超时时，将未返回响应的客户端连接请求传递给 upstream 中的下
        # 一个服务器
        proxy_next_upstream on;
        proxy_next_upstream_tries 3;        # 转发尝试请求最多 3 次
        proxy_next_upstream_timeout 10s;    # 总尝试超时时间为 10s
        proxy_socket_keepalive on;  # 开启 SO_KEEPALIVE 选项进行心跳检测
        proxy_pass backend;
    }
}
```

其中的参数及指令说明如下。

*   指令值参数 max_fails 是指 10s 内 Nginx 分配给当前服务器的连接失败次数累加值，每 10s 会重置为 0；
*   指令值参数 fail_timeout 既是失败计数的最大时间，又是服务器被置为失败状态的熔断时间，超过这个时间将再次被分配连接；
*   指令 proxy_connect_timeout 或 proxy_timeout 为超时状态时，都会触发 proxy_next_upstream 机制；
*   proxy_next_upstream 是 Nginx 下提高连接成功率的机制，当被代理服务器返回错误或超时时，将尝试转发给下一个可用的被代理服务器；
*   指令 proxy_next_upstream_tries 的指令值次数包括第一次转发请求的次数。

TCP 连接在接收到关闭连接通知前将一直保持连接，当 Nginx 与被代理服务器的两个连续成功的读或写操作的最大间隔时间超过 proxy_timeout 指令配置的时间时，连接将会被关闭。在 TCP 长连接的场景中，应适当调整 proxy_timeout 的设置，同时关注系统内核 SO_KEEPALIVE 选项的配置，可以防止过早地断开连接。