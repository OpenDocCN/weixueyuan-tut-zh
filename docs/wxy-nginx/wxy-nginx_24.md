# Nginx 镜像模块：ngx_http_mirror_module

> 原文：[`www.weixueyuan.net/a/676.html`](http://www.weixueyuan.net/a/676.html)

Nginx 中镜像模块的功能是将用户的访问请求镜像复制到指定的 URI，通过 location 的 URI 匹配将流量发送到指定的服务器。用户请求的实际请求响应通过 Nginx 返回客户端，镜像服务器的请求响应则会被 Nginx 服务器丢弃。

镜像请求与实际请求是异步处理的，对实际请求无影响。该模块的内置配置指令如下面表格中所示。

表：访问镜像指令

| 名称 | 访问镜像指令 |
| 指令 | mirror |
| 作用域 | http、server、location |
| 默认值 | off |
| 指令说明 | 将用户的访问请求镜像到指定的 URI，同级支持多个 URI |

配置样例如下：

server {
    listen 8080;
    root /opt/nginx-web/www;
    location / {
        mirror /benchmark;
        index index.html;
    }

    location = /benchmark {
        internal;
        proxy_pass http://192.168.2.145$request_uri;
    }
}

表：镜像请求体指令

| 名称 | 镜像请求体指令 |
| 指令 | mirror_request_body |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令值可选项 | on 或 off |
| 指令说明 | 将用户的访问请求体同步镜像到指定的 URI，当启用该指令时，创建镜像子请求前会优先读取并缓存客户端的请求体内容，同时 proxy_request_buffering、fastcgi_request_buffering、scgi_request_buffering 和 uwsgi_request_buffering 等指令的不缓存设置将被关闭 |

配置样例如下：

server {
    listen 8080;
    server_name localhost;
    root /opt/nginx-web/www;
    mirror_request_body off;
    location / {
        index index.html;
        mirror /accesslog;
    }

    location = /accesslog {
        internal;
        proxy_pass http://192.168.2.145/accesslog/${server_name}_$server_port$request_uri;
    }
}

如果该指令值为 off 则不同步请求体。

配置样例如下：

server {
    listen 8080;
    root /opt/nginx-web/www;
    location / {
        mirror /benchmark; # 镜像用户请求
        mirror /benchmark; # 镜像用户请求
        mirror /benchmark; # 镜像用户请求
        index index.html;
    }

    location = /benchmark {
        internal;
        proxy_pass http://192.168.2.145$request_uri;
    }
}

访问镜像模块可以将用户请求同步镜像到指定的服务器，同时还可以对用户的流量进行放大，通常可以在镜像线上流量后进行压力测试或预生产环境验证。