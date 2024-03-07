# Nginx PHP 服务器环境搭建

> 原文：[`www.weixueyuan.net/a/713.html`](http://www.weixueyuan.net/a/713.html)

CentOS 7 默认的 PHP 版本是 5.3，可以使用 Remi 扩展源安装 PHP 5.6 和 PHP-FPM。

```

yum install -y epel-release               # 安装 EPEL 扩展源
    # 安装 Remi 扩展源
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
yum install -y --nogpgcheck --enablerepo=remi --enablerepo=remi-php56 \
    php php-opcache php-devel php-mbstring php-mcrypt \
    php-mysqlnd php-phpunit-PHPUnit php-pecl-xdebug \
    php-pecl-xhprof php-gd php-ldap php-xml php-fpm \
    php-pecl-imagick              # 安装基于 Remi 扩展源的 PHP 5.6

systemctl start php-fpm           # 启动 PHP-FPM 服务
```

在 Nginx 的 conf 文件夹中创建文件 fscgi.conf，用于编辑 FastCGI 的全局配置，配置内容如下：

```

# 缓冲区配置
fastcgi_buffering on;             # 默认启用缓冲区
fastcgi_buffers 8 64k;            # 若响应数据大小小于 512KB，则会分配 8 个 64KB 缓冲区为其缓
                                  # 冲；若大于 512KB，则超出的部分会存储到临时文件中
fastcgi_buffer_size 64k;          # 读取 FastCGI 服务器响应数据第一部分的缓冲区大小为 64KB，
                                  # 通常包含响应头信息
fastcgi_busy_buffers_size 128K;   # 繁忙时向客户端发送响应的缓冲区大小为 128KB
fastcgi_limit_rate 0;             # 默认不做限制
fastcgi_max_temp_file_size 1024M; # 临时文件中大小为 1024MB
fastcgi_temp_file_write_size 64k; # 每次写入临时文件的数据大小为 64KB
# fastcgi_temp_path 使用默认配置

# 请求处理
fastcgi_request_buffering on;     # 默认启用读取整个请求体到缓冲区后再向 FastCGI 服务器发送请求
fastcgi_pass_request_body on;     # 默认将客户端请求体传递给 FastCGI 服务器
fastcgi_pass_request_headers on;  # 默认将客户端请求头传递给 FastCGI 服务器

# FastCGI 连接配置
fastcgi_connect_timeout 60s;      # 默认 Nginx 与 FastCGI 服务器建立连接的超时时间为 60s
fastcgi_keep_conn on;             # 启用保持连接
fastcgi_ignore_client_abort on;   # 当客户端关闭连接时，同时关闭与 FastCGI 服务器的连接
fastcgi_read_timeout 60s;         # 默认连续两个从 FastCGI 服务器接收数据的读操作之间的间隔
                                  # 时间为 60s
fastcgi_send_timeout 60s;         # 默认连续两个发送到 FastCGI 服务器的写操作之间的间隔时间
                                  # 为 60s
fastcgi_socket_keepalive on;      # FastCGI 的连接启用 so-keepalive socket 选项

# 响应处理
fastcgi_force_ranges on ;         # 强制启用 byte-range 请求支持
fastcgi_hide_header X-Powered-By; # 隐藏 PHP 版本字段
# fastcgi_pass_header 无必须传递的特殊头字段属性

fastcgi_ignore_headers X-Accel-Redirect X-Accel-Expires \
                       X-Accel-Limit-Rate X-Accel-Buffering \
                       X-Accel-Charset Expires \
                       Cache-Control Set-Cookie Vary;
                                  # 禁止 Nginx 处理从 FastCGI 获取响应的头属性字段

# 异常处理
fastcgi_intercept_errors on;      # 在 FastCGI 响应数据中响应码大于等于 300 时重定向给 Nginx
fastcgi_next_upstream   error timeout invalid_header \
                        http_500 http_503 http_403 \
                        http_404 http_429;  # 当出现指定的条件时，将用户请求传递给 upstream
                                            # 中的下一个服务器
fastcgi_next_upstream_timeout 0;            # 不限制将用户请求传递给 upstream 中的下一个
                                            # 服务器的超时时间
fastcgi_next_upstream_tries 0;              # 不限制将用户请求传递给 upstream 中的下一个
                                            # 服务器的尝试次数
```

Nginx PHP 网站配置如下：

```

server {
    listen 8080;
    root /opt/nginx-web/phpweb;
    index index.php;                        # 默认首页 index.php
    include fscgi.conf;                     # 引入 FastCGI 配置

    location ~ \.php(.*)$ {
        fastcgi_pass   127.0.0.1:9000;      # FastCGI 服务器地址及端口
        fastcgi_index  index.php;

        fastcgi_split_path_info    ^(.+\.php)(.*)$;   # 获取$fastcgi_path_info 变量值
        fastcgi_param PATH_INFO    $fastcgi_path_info; # 赋值给参数 PATH_INFO
        include        fastcgi.conf;                   # 引入默认参数文件
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```