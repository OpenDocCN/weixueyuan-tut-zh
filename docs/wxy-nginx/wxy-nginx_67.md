# Nginx 访问日志配置

> 原文：[`www.weixueyuan.net/a/793.html`](http://www.weixueyuan.net/a/793.html)

Nginx 的访问日志主要记录用户客户端的请求信息（见下表）。用户的每次请求都会记录在访问日志中，access_log 指令可以设置日志的输出方式及引用的日志格式。

| 名称 | 访问日志指令 |
| 指令 | access_log |
| 作用域 | http、stream、server、location、if in location、limit except |
| 默认值 | logs/access.log combined; |
| 指令值格式 | off 或 path[format[buffer=size][gzip[=level]][flush=time][if=condition]]; |
| 指令说明 | 设置访问日志输出方式及引用的日志格式 |

关于 access_log 指令有以下几点需要说明：

*   在同一级别的指令域中，也可指定多个日志；
*   指令值中的第一个参数用于设置输出日志的方式，默认是输出到本地的文件中。该指令也支持输出到 syslog 或内存缓冲区中；
*   该指令在 stream 指令域中时，默认值为 off；

access_log off;

*   参数 path，设置日志输出的文件路径或 syslog 服务器地址；

access_log logs/access.log combined;

*   参数 format，设置关联 log_format 指令定义的日志格式名；
*   参数 buffer，设置日志文件缓冲区大小。当缓冲区日志数据超出该值时，缓冲区日志数据会被写到磁盘文件。默认缓冲区大小为 64KB；
*   参数 flush，设置日志缓冲区刷新的时间间隔，缓冲区日志的保护时间超过这个设定值时，缓冲区日志数据会被写到磁盘文件；
*   参数 gzip，设置缓冲区数据的压缩级别，缓冲区数据会被压缩后再写出到磁盘文件。压缩级别范围 1～9，级别越高压缩比越高，系统资源消耗也最大，默认级别为 1；

access_log logs/log.gz combined gzip flush=5m;

*   参数 if，设置是否记录日志，当参数值的条件成立，即不为 0 或空时，才记录日志。

map $status $loggable {
    ~^[23]  0;
    default 1;
}

access_log logs/access.log combined if=$loggable;

日志格式指令如下表所示。

| 名称 | 日志格式指令 |
| 指令 | log_format |
| 作用域 | http、stream |
| 默认值 | combined"..."; |
| 指令值格式  | name[escape=default 或 json 或 none]string...; |
| 指令说明 | 设置访问日志输出方式及输出日志格式 |

关于 log_format 指令有以下几点需要说明：

*   指令值参数 name 用于设置日志格式名。该名称全局唯一，可以被 access_log 引用；
*   指令值参数 escape 用于设置日志输出字符串编码格式，json 支持中文字符内容输出；
*   指令值参数 string 用于设置日志输出格式字符串。该字符串由 Nginx 公共变量和仅在日志写入时存在的变量组成。HTTP 常用变量如下表所示。

| 变量名 | 变量说明 |
| $time_iso8601 | ISO 8601 时间格式 |
| $time_local | 用户请求的时间和时区 |
| $msec | 毫秒级别的日志记录时间 |
| $remote_addr | 发起与 Nginx 建立连接的网络客户端的 IP，有时会是上层代理服务器的 IP |
| $http_x_forwarded_for | 可以记录客户端 IP，通过代理服务器来记录客户端的 IP |
| $remote_user | 用于记录远程客户端的用户名称 |
| $http_user_agent | 用户客户端浏览器标识 |
| $connection | 网络连接编号 |
| $connection_requests | 当前连接的请求数 |
| $request | 用户请求的 URI 及请求方法 |
| $request_method | 用户请求方法 |
| $request_uri | 用户请求的 URI 及请求方法 |
| $server_protocol | 请求协议 |
| $request_time | 请求时间 |
| $request_length | 请求数据大小 |
| $status | 用户请求响应状态码 |
| $bytes_sent | 发送到客户端响应数据的大小 |
| $body_bytes_sent | 用户请求返回的响应体字节数 |
| $http_referer | HTTP 请求头中属性字段 referer |

配置样例如下：

```

# 普通格式日志
log_format  main  '$remote_addr - $connection - $remote_user [$time_local] "$request" - $upstream_addr'
                  '$status  - $body_bytes_sent - $request_time - "$http_referer" '
                  '"$http_user_agent" - "$http_x_forwarded_for" - ';

# JSON 格式日志
log_format json '{"@timestamp": "$time_iso8601", '
                '"connection": "$connection", '
                '"remote_addr": "$remote_addr", '
                '"remote_user": "$remote_user", '
                '"request_method": "$request_method", '
                '"request_uri": "$request_uri", '
                '"server_protocol": "$server_protocol", '
                '"status": "$status", '
                '"body_bytes_sent": "$body_bytes_sent", '
                '"http_referer": "$http_referer", '
                '"http_user_agent": "$http_user_agent", '
                '"http_x_forwarded_for": "$http_x_forwarded_for", '
                '"request_time": "$request_time"}';
```

Nginx TCP/UDP 的访问日志的变量与 HTTP 的访问日志的变量是不同的，TCP/UDP 常见日志变量如下表所示。

| 变量名 | 变量说明 |
| $time_iso8601 | ISO 8601 时间格式 |
| $time_local | 用户请求的时间和时区 |
| $connection | 网络连接编号 |
| $remote_addr | 发起与 Nginx 建立连接的网络客户端的 IP，有时会是上层代理服务器的 IP |
| $server_addr | Nginx 服务器地址 |
| $server_port | Nginx 服务器端口 |
| $status | 用户请求响应状态码 |
| $upstream_addr | 被代理服务器地址 |
| $bytes_received | 接收字节数 |
| $bytes_sent | 发送字节数 |
| $session_time | 连接会话时间 |
| $proxy_protocol_addr | 代理协议地址 |
| $proxy_protocol_port | 代理协议端口 |

Nginx 的 TCP/UDP 的日志处理是在连接处理阶段结束时才发生，所以 TCP/UDP 代理的访问日志只在连接关闭时才被记录。访问日志格式配置样例如下：

```

# 普通格式日志
log_format  tcp  '$remote_addr - $connection - [$time_local] $server_addr: $server_port '
                  '- $status - $upstream_addr - $bytes_received - $bytes_sent - $session_time '
                  '- $proxy_protocol_addr:$proxy_protocol_port ';

# JSON 格式日志
log_format json '{"@timestamp": "$time_iso8601", '
                '"connection": "$connection", '
                '"remote_addr": "$remote_addr", '
                '"server_addr": "$server_addr:$server_port" '
                '"status": "$status" '
                '"upstream_addr": "$upstream_addr" '
                '"bytes_received": "$bytes_received" '
                '"bytes_sent": "$bytes_sent" '
                '"session_time": "$session_time" '
                '"proxy_protocol_addr": "$proxy_protocol_addr:$proxy_protocol_port" '}'
```

打开日志缓存指令见下表。

| 名称 | 打开日志缓存指令 |
| 指令 | open_log_file_cache |
| 作用域 | http、stream、server、location |
| 默认值 | off |
| 指令值格式 | off 或 max=N [inactive=time][min_uses=N][valid=time]; |
| 指令说明 | 设置存储日志文件描述符（文件句柄）的缓存 |

关于 open_log_file_cache 指令有以下几点需要说明：

*   默认配置下，Nginx 每次将缓冲区日志数据保存到磁盘中，都需要先打开文件并获得文件描述符，然后向该文件描述符的文件中写入日志数据，最后关闭该文件描述符的文件。该指令把打开文件的文件描述符（文件句柄）存储在缓存中，进而提升写入日志的效率；
*   指令值 max 用于设置缓存中存储的文件描述符的最大数量，超过该值时，将按照 LRU 算法对缓存中文件描述符进行关闭；
*   指令值参数 inactive 用于设置缓存中每个文件描述符存活的时间，默认为 10s；
*   指令值参数 min_uses 用于设置可被缓存文件描述符的最小使用次数，默认为 1 次；
*   指令值参数 valid 用于设置缓存检查频率，默认为 60s；
*   指令值 off 用于关闭打开日志缓存的功能。

open_log_file_cache max=1000 inactive=20s valid=1m min_uses=2;
logs/access.log combined;