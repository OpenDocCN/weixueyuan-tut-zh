# Nginx limit_conn：并发连接数限制模块

> 原文：[`www.weixueyuan.net/a/688.html`](http://www.weixueyuan.net/a/688.html)

Nginx 中的并发连接数限制模块（ngx_http_limit_conn_module）能够对访问连接中含有指定变量且变量值相同的连接进行计数，指定的变量可以是客户端 IP 地址或请求的主机名等。当计数值达到 limit_conn 指令设定的值时，将会对超出并发连接数的连接请求返回指定的响应状态码（默认状态码为 503）。

该模块只会对请求头已经完全读取完毕的请求进行计数统计。由于 Nginx 采用的是多进程的架构，该模块通过共享内存存储计数状态以实现多个进程间的计数状态共享。该模块的内置配置指令如下面表格中所示。

表：计数存储区指令

| 名称 | 计数存储区指令 |
| 指令 | limit_conn_zone |
| 作用域 | http |
| 默认值 | -- |
| 指令说明 | 设定用于存储指定变量计数的共享内存区域 |

表：连接数设置指令

| 名称 | 连接数设置指令 |
| 指令 | limit_conn |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 设置指定变量的最大并发连接数 |

表：连接数日志级别指令

| 名称 | 连接数日志级别指令 |
| 指令 | limit_conn_log_level |
| 作用域 | http、server、location |
| 默认值 | error |
| 指令值可选项 | info、notice、warn、error |
| 指令说明 | 当指定变量的并发连接数达到最大值时，输出日志的级别 |

表：连接数状态指令

| 名称 | 连接数状态指令 |
| 指令 | limit_conn_status |
| 作用域 | http、server、location |
| 默认值 | 503 |
| 指令说明 | 当指定变量的并发连接数达到最大值时，请求返回的状态码 |

配置样例如下：

limit_conn_zone $binary_remote_addr zone=addr:10m;  # 对用户 IP 进行并发计数，将计数内存区命
                                                                                           # 名为 addr，设置计数内存区大小为 10MB
server {
    location /web1/ {
        limit_conn addr 1;                                                      # 限制用户的并发连接数为 1
    }
}

limit_conn_zone 的格式为 limit_conn_zone key zone=name:size。

limit_conn_zone 的 key 可以是文本、变量或文本与变量的组合。

$binary_remote_addr 为 IPv4 时占用 4B，为 IPv6 时占用 16B。

limit_conn_zone 中 1MB 的内存空间可以存储 32 000 个 32B 或 16 000 个 64B 的变量计数状态。

变量计数状态在 32 位系统平台占用 32B 或 64B，在 64 位系统平台占用 64B。

并发连接数同样支持多个变量的同时统计，配置样例如下：

limit_conn_zone $binary_remote_addr zone=perip:10m;
limit_conn_zone $server_name zone=perserver:10m;

server {
    ...
    limit_conn perip 10;
    limit_conn perserver 100;
}