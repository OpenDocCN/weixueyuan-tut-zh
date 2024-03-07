# Nginx HTTP2 模块配置简述

> 原文：[`www.weixueyuan.net/a/734.html`](http://www.weixueyuan.net/a/734.html)

HTTP/2 是 HTTP 协议的 2.0 版本，该协议通过多路复用、请求优化、HTTP 头压缩等功能提升网络传输速度、优化用户体验。HTTP/2 使用二进制分帧层将传输的数据分割为更小的数据和帧，并对它们进行二进制格式编码处理，以实现在不改变 HTTP 现有语义等标准的基础上提升传输性能，从而降低响应延迟、提高请求吞吐的能力。HTTP/2 通过多路复用技术使客户端可以并行发送多个请求，以提高带宽的利用率。

HTTP/2 是基于 SPDY 协议设计的，是 SPDY 的演进版本，但其不强制使用 HTTPS 协议，仍可支持 HTTP 明文传输。Nginx 是通过 ngx_http_v2_module 实现 HTTP/2 协议支持的，编译配置时可通过增加参数 --with-http_v2_module 启用 HTTP2 模块。

HTTP2 模块配置指令如下。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| http2_body_preread_size | size | 64k | 设置每个请求可用缓冲区的大小，可用于保存请求体数据 |
| http2_chunk_size | size | 8k | 设置响应体被分割成块的大小，该指令值设置得太小时将消耗服务器资源，太大时将因队列头部阻塞（Head-of-line Blocking，HOL）影响优先级 |
| http2_idle_timeout | time | 3m | 设置关闭连接后非活动连接超时时间 |
| http2_max_concurrent_pushes | number | 10 | 设置连接中并发推送请求的最大数 |
| http2_max_concurrent_streams | number | 128 | 设置连接中并发 HTTP/2 流的最大数 |
| http2_max_field_size | size | 4k | 设置 HPACK 压缩请求头字段的最大大小，如果应用了 Huffman 编码，解压缩后的名称和值字符串的实际大小可能会更大 |
| http2_max_header_size | size | 16k | 设置 HPACK 解压缩后整个请求头列表的最大大小 |
| http2_max_requests | number | 1000 | 设置一个连接多路复用最大请求数 |
| http2_push | uri 或 off | off | 设置服务端主动推送的资源 URI，指令值 uri 是推送资源的网站绝对路径 |
| http2_push_preload | on 或 off | off | 设置当响应头中有 Link 字段时，是否自动向客户端推送 Link 字段中设置的网站资源 |
| http2_recv_buffer_size | size | 256k | 设置 Nginx 每个工作进程接收缓冲区的大小 |
| http2_recv_timeout | time | 30s | 设置等待客户端发送更多数据的超时时间，超过设定时间之后关闭连接 |

关于上面的表格，有以下几点需要说明。

*   http2_recv_buffer_size 指令可编写在 http 指令域中；
*   http2_chunk_size、http2_push、http2_push_preload 指令可编写在 http、server、location 指令域中；
*   其余的指令可编写在 http、server 指令域中。

HTTP2 服务器推送可以实现将多个资源文件（CSS、JS、图片等）同时发送到客户端，如下页面中包含 style.css 和 nginx.png 两个资源文件。

```

<!DOCTYPE html>
<html>
<meta charset="utf-8">
<title>Nginx HTTPv2 Test</title>
<head>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Nginx HTTPv2 Test</h1>
    <img src="nignx.png">
</body>
</html>
```

在没有服务器推送的情况下，客户端通过 3 个 GET 方法获取该页面的所有资源。在启用服务器推送后客户端只需通过一个 GET 方法，就可以获取到该页面的所有资源。配置样例如下：

```

server {
    listen 443 ssl http2 default_server;

    ssl_certificate ssl/www_nginxbar_org.pem;       # 网站证书文件
    ssl_certificate_key ssl/www_nginxbar_org.key;   # 网站证书密钥文件
    ssl_password_file ssl/www_nginxbar_org.pass;    # 网站证书密钥密码文件
    root /opt/nginx-web;
    location / {
        http2_push /style.css                       # 服务端推送
        http2_push /nginx.png                       # 服务端推送
    }
}
```