# Nginx 反向代理缓存服务器配置

> 原文：[`www.weixueyuan.net/a/768.html`](http://www.weixueyuan.net/a/768.html)

Nginx 代理功能根据应用方式的不同分为正向代理和反向代理，Nginx 开源版本的正向代理功能并不完整，不支持 HTTP 的 CONNECT 方法，所以 HTTPS 的正向代理功能通常是使用第三方模块来实现的。

Nginx 的 HTTPS 正向代理使用最多的第三方模块是 ngx_http_proxy_connect_module，但其不支持缓存，所以开源版本 Nginx 无法在正向代理缓存的使用场景中应用。

Nginx 的重点缓存应用是在反向代理缓存的应用场景，官方也一直在不断地增强该功能。Nginx 反向代理缓存是目前网站架构中最常用的缓存方式，其不仅被网站架设者用以提高访问速度，降低应用服务器的负载，同时也被广泛应用于 CDN 的缓存服务器中。

Nginx 的反向代理缓存有以下几个功能特点。

*   故障降级。如果源服务器因故障停机，即便缓存过期，也可以被返回给用户使用，这就避免了页面无法打开的故障信息传递，从而实现有效容错降级；
*   缓存负载。基于 Nginx 提供的比例分配赋值指令，可以将请求分配给由多个不同的硬盘组成的缓存池，以实现缓存存储负载，降低 I/O 瓶颈，提升磁盘效率；
*   缓存锁。使多个相同的请求只有一个可以访问被代理服务器，其他的请求则等待缓存生成后，从缓存中获取响应数据，从而有效地提升缓存利用率，降低被代理服务器的负载；
*   缓存验证支持。支持在 Nginx 本地缓存有效期过期后，通过服务器远端验证的方式确认缓存是否有效；
*   范围请求支持。通过切片指令设置，提升了范围请求的缓存效率，使其响应速度更快；
*   缓存控制。可对用户的请求是否使用缓存、响应数据是否被缓存、可被缓存的最低使用频率等方式实现缓存控制。

配置样例如下：

```

upstream backend_server {
    ip_hash;                                 # session 会话保持
    server 192.168.2.145:8081;               # 被代理服务器 IP
    server 192.168.2.159:8081;               # 被代理服务器 IP
}

proxy_cache_path /usr/local/nginx/nginx-cache1
                            levels=1:2
                            keys_zone=cache_hdd1:100m
                            max_size=10g
                            use_temp_path=off
                            inactive=60m;    # 设置缓存存储路径 1，缓存的共享内存名称和大小 100MB，
                                             # 无效缓存的判断时间为 1 小时

proxy_cache_path /usr/local/nginx/nginx-cache2
                            levels=1:2
                            keys_zone=cache_hdd2:100m
                            max_size=10g
                            use_temp_path=off
                            inactive=60m;    # 设置缓存存储路径 2，缓存的共享内存名称和大小 100MB，
                                             # 无效缓存的判断时间为 1 小时

split_clients $request_uri $proxy_cache {
              50%          "cache_hdd1";     # 50%请求的缓存存储在第一个磁盘上
              50%          "cache_hdd2";     # 50%请求的缓存存储在第二个磁盘上
}

server {
    listen 8080;
    root /opt/nginx-web/phpweb;
    index index.php;
    include        proxy.conf;               # 引入默认配置文件

    location ~ \.(gif|jpg|png|htm|html|css|js|flv|ico|swf)(.*) {
                                                # 设置客户端静态资源文件缓存过期时间为 12 小时
        expires      12h;
    }

    proxy_ignore_headers Cache-Control Set-Cookie;
                                                # 忽略被代理服务器返回响应头中指定字段的控制响应
    location ~ / {
        root /opt/nginx-web/phpweb;

        proxy_cache $proxy_cache;     # 启用 proxy_cache_path 设置的$proxy_cache 的共享内存区域
        proxy_cache_lock on;          # 启用缓存锁
        proxy_cache_lock_age 5s;      # 缓存锁有效期为 5s
        proxy_cache_lock_timeout 5s;  # 等待缓存锁超时时间为 5s
        proxy_cache_methods GET HEAD; # 默认对 GET 及 HEAD 方法的请求进行缓存
        proxy_cache_min_uses 1;       # 响应数据至少被请求 1 次，才将被缓存

        proxy_cache_bypass $http_pragma;    # 当客户端请求头包含字段 pragma 时，不适用缓存

        proxy_cache_use_stale error timeout invalid_header
                                updating http_500 http_503
                                http_403 http_404 http_429;
                                        　　　　# 当出现指定条件时，使用已经过期的缓存响应数据

        proxy_cache_background_update on;   # 允许使用过期的响应数据时，启用后台子请求用以更新过
                                            # 期缓存，并将过期的缓存响应数据返回给客户端

        proxy_cache_revalidate on;          # 当缓存过期时，向后端服务器发起服务端校验
        proxy_cache_valid 200 301 302 10h;  # 200 301 302 状态码的响应缓存 10 小时
        proxy_cache_valid any 1m;           # 其他状态码的响应缓存 1 分钟

        add_header X-Cache-Status $upstream_cache_status;
                                               # 添加缓存请求状态标识

        proxy_pass   http://backend_server;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```

在默认配置下，Nginx 会对被代理服务器返回响应数据信息头的缓存控制字段 Cache-Control 执行相关操作。当 Cache-Control 字段的值为 private、no-cache 或者有字段 Set-Cookie 时，它会对响应数据缓存产生影响，可以使用 proxy_ignore_headers 指令忽略这些字段的操作响应。

proxy_ignore_headers Cache-Control Set-Cookie;

Nginx 默认只对 GET 和 HEAD 方法的请求进行缓存，如果想对 POST 请求方法的数据进行缓存，则可以使用 proxy_cache_methods 指令进行设置。

proxy_cache_methods GET HEAD POST;