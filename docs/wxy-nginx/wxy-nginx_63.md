# Nginx upstream 容错机制详解

> 原文：[`www.weixueyuan.net/a/785.html`](http://www.weixueyuan.net/a/785.html)

Nginx 在 upstream 模块中默认的检测机制是通过用户的真实请求去检查被代理服务器的可用性，这是一种被动的检测机制，通过 upstream 模块中 server 指令的指令值参数 max_fails 及 fail_timeout 实现对被代理服务器的检测和熔断。

配置样例如下：

```

upstream http_backend {
    # 10s 内出现 3 次错误，该服务器将被熔断 10s
    server 192.168.2.154:8080 max_fails=3 fail_timeout=10s;
    server 192.168.2.109:8080 max_fails=3 fail_timeout=10s;
    server 192.168.2.108:8080 max_fails=3 fail_timeout=10s;
    server 192.168.2.107:8080 max_fails=3 fail_timeout=10s;
}

server {
    proxy_connect_timeout 5s;               # 与被代理服务器建立连接的超时时间为 5s
    proxy_read_timeout 10s;                 # 获取被代理服务器的响应最大超时时间为 10s

    # 当与被代理服务器通信出现指令值指定的情况时，认为被代理出错，并将请求转发给上游服务器组中
    # 的下一个可用服务器
    proxy_next_upstream http_502 http_504 http_404 error timeout invalid_header;
    proxy_next_upstream_tries 3;            # 转发请求最多 3 次
    proxy_next_upstream_timeout 10s;        # 总尝试超时时间为 10s

    location /http/ {
        proxy_pass http://http_backend;
    }
}
```

其中的参数和指令说明如下。

*   指令值参数 max_fails 是指 10s 内 Nginx 分配给当前服务器的请求失败次数累加值，每 10s 会重置为 0；
*   指令值参数 fail_timeout 既是失败计数的最大时间，又是服务器被置为失败状态的熔断时间，超过这个时间将再次被分配请求；
*   指令 proxy_connect_timeout 或 proxy_read_timeout 为超时状态时，都会触发 proxy_next_upstream 的 timeout 条件；
*   proxy_next_upstream 是 Nginx 下提高请求成功率的机制，当被代理服务器返回错误并符合 proxy_next_upstream 指令值设置的条件时，将尝试转发给下一个可用的被代理服务器；
*   指令 proxy_next_upstream_tries 的指令值次数包括第一次转发请求的次数。

Nginx 被动检测机制的优点是不需要增加额外进程进行健康检测，但用该方法检测是不准确的。如当响应超时时，有可能是被代理服务器故障，也可能是业务响应慢引起的。如果是被代理服务器故障，那么 Nginx 仍会在一定时间内将客户端的请求转发给该服务器，用以判断其是否恢复。

Nginx 官方的主动健康检测模块仅集成在商业版本中，对于开源版本，推荐使用 Nginx 扩展版 OpenResty 中的健康检测模块 lua-resty-upstream-healthcheck。该模块的检测参数如下表所示。

| 参数 | 默认值 | 参数说明 |
| shm | -- | 指定用于健康检测的共享内存名称，共享内存名称由 lua_shared_dict 设定 |
| upstream | -- | 指定要做健康检查的 upstream 组名 |
| type | http | 指定检测的协议类型，目前只支持 http |
| http_req | -- | 指定用于健康探测的 raw 格式 http 请求字符串 |
| timeout | 1000 | 检测请求超时时间，单位为 ms |
| interval | 1000 | 健康检测的时间间隔，单位为 ms |
| valid_status | -- | 健康检测请求返回的合法响应码列表，比如 {200, 302} |
| concurrency | 1 | 健康检测请求的并发数，建议大于上游服务器组中的节点数 |
| fall | 5 | 对 UP 状态的设备，连续 fall 次失败，认定为 DOWN |
| rise | 2 | 对 DOWN 状态的设备，连续 rise 次成功，认定为 UP |
| version | 0 | 每次执行健康检测时的版本号，有节点状态改变，版本号加 1 |

模块 lua-resty-upstream-healthcheck 的原理是每到（interval）设定的时间，就会对被代理服务器的 HTTP 端口主动发起 GET 请求（http_req），当请求的响应状态码在确定为合法的列表（valid_status）中出现时，则认为被代理服务器是健康的，如果请求的连续（fall）设定次数返回响应状态码都未在列表（valid_status）中出现，则认为是故障状态。

对处于故障状态的设备，该模块会将其置为 DOWN 状态，直到请求的连续（rise）次返回的状态码都在确定为合法的列表中出现，被代理服务器才会被置为 UP 状态，并获得 Nginx 分配的请求，Nginx 在整个运行过程中不会将请求分配给 DOWN 状态的被代理服务器。

lua-resty-upstream-healthcheck 模块只会使用 Nginx 中的一个工作进程对被代理服务器进行检测，不会对被代理服务器产生大量的重复检测。

配置样例如下：

```

http {
    # 关闭 socket 错误日志
    lua_socket_log_errors off;

    # 上游服务器组样例
    upstream foo.com {
        server 127.0.0.1:12354;
        server 127.0.0.1:12355;
        server 127.0.0.1:12356 backup;
    }

    # 设置共享内存名称及大小
    lua_shared_dict _foo_zone 1m;

    init_worker_by_lua_block {
        # 引用 resty.upstream.health-check 模块
        local hc = require "resty.upstream.healthcheck"

        local ok, err = hc.spawn_checker{
            shm = "_foo_zone",              # 绑定 lua_shared_dict 定义的共享内存
            upstream = "foo.com",           # 绑定 upstream 指令域
            type = "http",

            http_req = "GET /status HTTP/1.0\r\nHost: foo.com\r\n\r\n",
                                                # 用以检测的 raw 格式 http 请求

            interval = 2000,                # 每 2s 检测一次
            timeout = 1000,                 # 检测请求超时时间为 1s
            fall = 3,                       # 连续失败 3 次，被检测节点被置为 DOWN 状态
            rise = 2,                       # 连续成功 2 次，被检测节点被置为 UP 状态
            valid_statuses = {200, 302},    # 当健康检测请求返回的响应码为 200 或 302 时，被认
                                                # 为检测通过
            concurrency = 10,               # 健康检测请求的并发数为 10
        }
        if not ok then
            ngx.log(ngx.ERR, "failed to spawn health checker: ", err)
            return
        end
    }

    server {
        listen 10080;
        access_log  off;                    # 关闭 access 日志输出
        error_log  off;                     # 关闭 error 日志输出

        # 健康检测状态页
        location = /healthcheck {
            allow 127.0.0.1;
            deny all;

            default_type text/plain;
            content_by_lua_block {
                # 引用 resty.upstream.healthcheck 模块
                local hc = require "resty.upstream.healthcheck"
                ngx.say("Nginx Worker PID: ", ngx.worker.pid())
                ngx.print(hc.status_page())
            }
        }
    }
}
```

以下是对该配置样例的几点说明。

*   该配置样例参照 OpenResty 官方样例简单修改；
*   对不同的 upstream 需要通过参数 upstream 进行绑定；
*   建议为每个上游服务器组指定独享的共享内存，并用参数 shm 进行绑定。