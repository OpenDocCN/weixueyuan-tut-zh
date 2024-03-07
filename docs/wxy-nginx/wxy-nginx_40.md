# Nginx 基于 FastCGI 实现负载均衡

> 原文：[`www.weixueyuan.net/a/717.html`](http://www.weixueyuan.net/a/717.html)

Nginx 支持后端多个 FastCGI 服务器的负载均衡，负载均衡有两种方式：一种是通过域名解析多个 FastCGI 服务器，该方式通过所有域名地址轮询（round-robin）的方式实现负载；另一种是通过配置 Nginx 的 upstream 模块实现负载。

本节通过后一种方式实现负载均衡场景的搭建。Nginx 的 FastCGI 模块支持对后端 PHP 解析数据的缓存，对于动态数据的缓存可以在实际应用场景中提升动态网站的访问速度。

安装 PHP-FPM 后，如果把 PHP 代码部署在与 Nginx 不同的服务器上，需要修改 PHP-FPM 服务器中的 /etc/php-fpm.d/www.conf 配置。

    # PHP-FPM 绑定本机所有 IP
sed -i "s/^listen =.*/listen = 0.0.0.0:9000/g" /etc/php-fpm.d/www.conf    
    # 允许任何主机访问 PHP-FPM 服务
sed -i "s/^listen.allowed_clients/;listen.allowed_clients/g" /etc/php-fpm.d/www.conf

Nginx 配置样例如下：

```

upstream fscgi_server {
    ip_hash;                               # session 会话保持
    server 192.168.2.145:9000;             # PHP-FPM 服务器 IP
    server 192.168.2.159:9000;             # PHP-FPM 服务器 IP
}

fastcgi_cache_path /usr/local/nginx/nginx-cache1
                            levels=1:2
                            keys_zone=fscgi_hdd1:100m
                            max_size=10g
                            use_temp_path=off
                            inactive=60m;    # 设置缓存存储路径 1，缓存的共享内存名称和大小
                                             # 100MB，无效缓存的判断时间为 1 小时

fastcgi_cache_path /usr/local/nginx/nginx-cache2
                            levels=1:2
                            keys_zone=fscgi_hdd2:100m
                            max_size=10g
                            use_temp_path=off
                            inactive=60m;   # 设置缓存存储路径 2，缓存的共享内存名称和大小
                                            # 100MB，无效缓存的判断时间为 1 小时

split_clients $request_uri $fscgi_cache {
              50%           "fscgi_hdd1";  # 50%请求的缓存存储在第一个磁盘上
              50%           "fscgi_hdd2";  # 50%请求的缓存存储在第二个磁盘上
}

server {
    listen 8080;
    root /opt/nginx-web/phpweb;
    index index.php;
    include        fscgi.conf;             # 引入默认配置文件

    location ~ \.(gif|jpg|png|htm|html|css|js|flv|ico|swf)(.*) {  # 静态资源文件过期时间
                                                                  # 为 12 小时
        expires      12h;
    }

    set $no_cache 0;
    if ($query_string != "") {              # URI 无参数的数据不进行缓存
        set $no_cache 1;
    }

    location ~ \.php(.*)$ {
        root /opt/nginx-web/phpweb;

        fastcgi_cache $fscgi_cache;        # 启用 fastcgi_cache_path 设置的$fscgi_cache
                                           # 的共享内存区域做缓存
        fastcgi_cache_key ${request_method}://$host$request_uri; # 设置缓存的关键字
        fastcgi_cache_lock on;             # 启用缓存锁
        fastcgi_cache_lock_age 5s;         # 启用缓存锁时，添加缓存请求的处理时间为 5s
        fastcgi_cache_lock_timeout 5s;     # 等待缓存锁超时时间为 5s
        fastcgi_cache_methods GET HEAD;    # 默认对 GET 及 HEAD 方法的请求进行缓存
        fastcgi_cache_min_uses 1;          # 响应数据被请求一次就将被缓存

        fastcgi_no_cache $no_cache;        # $no_cache 时对当前请求不进行缓存
        fastcgi_cache_bypass $no_cache;    # $no_cache 时对当前请求不进行缓存

        fastcgi_cache_use_stale error timeout invalid_header
                                updating http_500 http_503
                                http_403 http_404 http_429;        # 当出现指定的条件时，使用
                                                                   # 已经过期的缓存响应数据
        fastcgi_cache_background_update on; # 允许使用过期的响应数据时，启用后台子请求用于
                                            # 更新过期缓存，并将过期的缓存响应数据返回给客户端

        fastcgi_cache_revalidate on;       # 当缓存过期时，向后端服务器发起包含 If-
                                           # Modified-Since 和 If-None-Match HTTP 消息
                                           # 头字段的服务端校验
        fastcgi_cache_valid 200 301 302 10h; # 200 301 302 状态码的响应缓存 10 小时
        fastcgi_cache_valid any 1m;        # 其他状态码的响应缓存 1 分钟

        add_header X-Cache-Status $upstream_cache_status;   # 查看缓存命中状态

        fastcgi_pass   fscgi_server;
        fastcgi_index  index.php;
        fastcgi_split_path_info       ^(.+\.php)(.*)$;      # 获取$fastcgi_path_info 变量值
        fastcgi_param PATH_INFO       $fastcgi_path_info;   # 赋值给参数 PATH_INFO
        include        fastcgi.conf;                        # 引入默认参数文件
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```