# Nginx 请求频率限制模块

> 原文：[`www.weixueyuan.net/a/692.html`](http://www.weixueyuan.net/a/692.html)

Nginx 的请求频率限制模块（ngx_http_limit_req_module）会对指定变量的请求次数进行计数，当该变量在单位时间内的请求次数超过设定的数值时，后续请求会被延时处理，当被延时处理的请求数超过指定的队列数时，将返回指定的状态码（默认状态码为 503）。

通常该模块被用于限定同一 IP 客户端单位时间内请求的次数。该模块通过共享内存存储计数状态以实现多个工作进程间的同一变量计数状态的共享。该模块的内置配置指令如下面表格中所示。

表：计数存储区指令

| 名称 | 计数存储区指令 |
| 指令 | limit_req_zone |
| 作用域 | http |
| 默认值 | -- |
| 指令说明 | 设定用于存储指定变量请求计数的共享内存区域 |

表：请求限制设置指令

| 名称 | 请求限制设置指令 |
| 指令 | limit_req |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 启用请求限制并进行请求限制的相关配置 |

表：请求限制日志级别指令

| 名称 | 请求限制日志级别指令 |
| 指令 | limit_req_log_level |
| 作用域 | http, server, location |
| 默认值 | error |
| 指令值可选项 | info, notice, warn, error |
| 指令说明 | 当指定变量的并发连接数达到最大值时，输出日志的级别 |

表：请求限制状态指令

| 名称 | 请求限制状态指令 |
| 指令 | limit_req_status |
| 作用域 | http, server, location |
| 默认值 | 503 |
| 指令说明 | 当指定变量的并发连接数达到最大值时，请求返回的状态码 |

配置样例如下：

http {
    limit_req_zone $server_name zone=addr:10m rate=1r/s;
                # 限制访问当前站点的请求数，对站点请求计数，将计数内存区命名为 addr，
                # 设置计数内存区大小为 10MB，请求限制为 1 秒 1 次
    server {
        location /search/ {
            limit_req zone=one;       
                # 同一秒只接收一个请求，其余的立即返回状态码 503，直到第 2 秒才接收新的请求
            limit_req zone=one burst=5;
                # 同一秒接收 6 个请求，其余的返回状态码 503，只处理一个请求，其余 5 个请求进入队
　　　　　　　　　 # 列，每秒向 Nginx 释放一个请求进行处理，同时允许接收一个新的请求进入队列
            limit_req zone=one burst=5 nodelay; 
                # 同一秒接收 6 个请求，其余的返回状态码 503，同时处理 6 个请求，6 秒后再接收新的请求
        }
    }
}

limit_req_zone 的 rate 参数的作用是对请求频率进行限制，有 r/s（每秒的请求次数）和 r/m（每分钟的请求次数）两个频率单位，也可根据每秒的次数换算成毫秒单位的次数。1MB 内存大小大约可以存储 16000 个 IP 地址的状态信息。

limit_req 的 burst 参数相当于一个缓冲容器，该容器内可容纳 burst 所设置的数量的请求，没有 nodelay 参数时，将匀速向 Nginx 释放需要处理的请求。未进入 burst 容器队列的请求将被返回状态码 503 或由 limit_req_status 指令指定的状态码。

limit_req 的 nodelay 参数是指对请求队列中的请求不进行延时等待，而是立即处理。

请求频率同样支持多个变量的同时计数及叠加，配置样例如下：

limit_req_zone $binary_remote_addr zone=perip:10m rate=1r/s;
limit_req_zone $server_name zone=perserver:10m rate=10r/s;  

server {
    ...
    limit_req zone=perip burst=5 nodelay;
    limit_req zone=perserver burst=10;
}