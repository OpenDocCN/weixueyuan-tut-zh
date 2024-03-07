# Nginx 开启 gzip 压缩及相关配置详解

> 原文：[`www.weixueyuan.net/a/702.html`](http://www.weixueyuan.net/a/702.html)

为提高用户获取响应数据的速度，Nginx 服务器可以将响应数据进行 gzip 压缩，在减小响应数据的大小后再发送给用户端浏览器，相对于使用户浏览 Web 页面，上述方式显示速度更快。

要想启用响应数据 gzip 压缩（ngx_http_gzip_module 模块）功能，需要用户浏览器也支持 gzip 解压功能，目前大多数浏览器都支持 gzip 压缩数据的显示。Nginx 服务器接收客户端浏览器发送的请求后，通过请求头中的属性字段 Accept-Encoding 判断浏览器是否支持 gzip 压缩，对支持 gzip 压缩的浏览器将发送 gzip 压缩的响应数据。

ngx_http_gzip_module 模块的内置配置参数如下表所示。

| 指令 | 作用域 | 默认值 | 指令值可选项 | 指令说明 |
| gzip | http、server、location、if in location | off | on 或 off | 启用 gzip 功能 |
| gzip_buffers | http、server、location | 32 4k 或 16 8k | -- | 设置 gzip 压缩缓冲区 |
| gzip_comp_level | http、server、location | 1 | -- | 设置 gzip 压缩级别，取值范围为 1~9，该指令值越大，压缩程度越高 |
| gzip_disable | http、server、location | -- | -- | 当请求头中的属性字段 User-Agent 的内容与指令值正则匹配时关闭 gzip 压缩功能 |
| gzip_http_version | http、server、location | 1.1 | 1.0 或 1.1 | 设置压缩请求的最早 HTTP 协议版本 |
| gzip_min_length | http、server、location | 20 | -- | 设置启用 gzip 压缩的响应数据的最小长度，判断依据为响应头中 Content-Length 的值。如果 Content-Length 不存在，则该指令无效；如果指令值为 0，则表示全部压缩 |
| gzip_proxied | http、server、location | off | off 或 expired 或 no-cache 或 no-store 或 private 或 no_last_modified 或 no_etag 或 auth 或 any | 根据被代理服务器返回响应数据的响应头属性字段判断是否启用 gzip 压缩 |
| gzip_types | http、server、location | text/html | -- | 设置可进行 gzip 压缩的响应数据的 MIME 类型，指令值为 * 时表示所有 MIME 类型 |
| gzip_vary | http、server、location | off | on 或 off | 在响应头中添加 Vary:Accept-Encoding，返回给前端代理或 CDN 服务器，用于判岗是否向客户端发送 gzip 的缓存副本，避免代理或 CDN 服务器将 gzip 压缩后的缓存副本响应给不具备 gzip 解压能力的浏览器 |

其中 gzip_proxied 指令的指令值可选项说明如下：

*   off：关闭该指令功能；
*   expired：若 HTTP 响应头中包含属性字段 Expires，则启用压缩；
*   no-cache：若 HTTP 响应头中包含属性字段 Cache-Control:no-cache，则启用压缩；
*   no-store：若 HTTP 响应头中包含属性字段 Cache-Control:no-store，则启用压缩；
*   private：若 HTTP 响应头中包含属性字段 Cache-Control:private，则启用压缩；
*   no_last_modified：若 HTTP 响应头中不包含属性字段 Last-Modified，则启用压缩；
*   no_etag：若 HTTP 响应头中不包含属性字段 ETag，则启用压缩；
*   auth：若 HTTP 响应头中包含属性字段 Authorization，则启用压缩；
*   any：对所有响应数据启用压缩。

当客户端浏览器不支持 gzip 压缩时，可以使用 ngx_http_gunzip_module 模块将压缩的数据解压后发送给客户端。对支持 gzip 压缩的浏览器不做处理。

ngx_http_gunzip_module 模块的内置配置指令如下表所示。

| 指令 | 作用域 | 默认值 | 指令值可选项 | 指令说明 |
| gunzip | http、server、location | off  | on 或  off  | 设置是否启用动态解压支持 |
| gunzip_buffers | http、server、location | 32 4K | -- | 设置用于解压的缓冲区大小 |

通常 gzip 压缩指令都是读取未压缩的文本，在进行动态压缩后把响应数据发送给客户端，ngx_http_gzip_static_module 模块可以使 Nginx 把 gzip 压缩过的以 .gz 为后缀的文件或已压缩的响应数据直接发送给客户端。

ngx_http_gzip_static_module 模块的内置配置指令如下表所示。

| 名称 | 静态压缩指令 |
| 指令 | gzip_static |
| 作用域 | http、server、location |
| 默认值 | off |
| 指令值可选项 | on 或 off 或 always |
| 指令说明 | 启用压缩数据读取功能 |

gzip_static 指令的指令值可选项说明如下：

*   on：不检查客户端是否支持 gzip 压缩数据，始终发送 gzip 压缩数据；
*   always：不检查客户端是否支持 gzip 压缩数据，始终发送 gzip 压缩数据；
*   该指令的执行优先级高于 gzip 指令；
*   开启该指令后，默认优先查找以 .gz 为后缀的文件；
*   gzip_types 指令对 gzip_static 的设置无效。

配置样例如下：

```

gzip_static always;                  # 始终发送静态的 gzip 压缩数据
gunzip on;                           # 若客户端浏览器不支持 gzip 压缩数据，则解压后发送
gunzip_buffers 16 8k;                                         # 解压缓冲区大小为 128KB
gzip_proxied expired no-cache no-store private auth;    # 当被代理的服务器符合条件时，
                                                        # 对响应数据启用 gzip 压缩

gzip on;                             # 启用动态 gzip 压缩功能
gzip_min_length  1k;                 # 响应数据超过 1KB 时启用 gzip 压缩
gzip_buffers     4 16k;              # 动态压缩的缓冲区大小是 64KB
gzip_comp_level 3;                   # 压缩级别为 3
gzip_types       text/plain application/x-javascript
                text/css application/xml text/javascript
                application/x-httpd-php image/jpeg
                image/gif image/png; # 对指定的 MIME 类型数据启用动态压缩
gzip_vary on;                        # 向前端代理或缓存服务器发送添加“Vary: Accept-
                                     # Encoding”的响应数据
```