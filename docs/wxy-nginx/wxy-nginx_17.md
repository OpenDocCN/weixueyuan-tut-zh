# Nginx 访问控制简述

> 原文：[`www.weixueyuan.net/a/660.html`](http://www.weixueyuan.net/a/660.html)

HTTP 核心配置指令中提供了基本的禁止访问、传输限速、内部访问控制等功能配置。配置指令如下表所示。

表：请求方法排除限制指令

| 名称 | 请求方法排除限制指令 |
| 指令 | limit_except |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 对指定方法以外的所有请求方法进行限定 |

配置样例如下：

http{
    limit_except GET {
        allow 192.168.1.0/24; # 允许 192.168.1.0/24 范围的 IP 使用非 GET 的方法
        deny all; # 禁止其他所有来源 IP 的非 GET 请求
    }
}

表：组合授权控制指令

| 名称 | 组合授权控制指令 |
| 指令 | satisfy |
| 作用域 | http, server, location |
| 默认值 | all |
| 指令值选项 | all 或 any |
| 指令说明 | 默认情况下，在响应客户端请求时，当 ngx_http_access_module、ngx_http_auth_basic_module、ngx_http_auth_request_module、ngx_http_auth_jwt_module 模块被限定的访问控制条件都符合时，才允许授权访问。当指令值为 any 时，ngx_http_access_module、ngx_http_auth_basic_module、ngx_http_auth_request_module、ngx_http_auth_jwt_module 模块的访问控制条件，符合任意一个，则认为可以授权访问 |

配置样例如下：

location / {
    satisfy any;

    allow 192.168.1.0/32;
    deny  all;

    auth_basic           "closed site";
    auth_basic_user_file conf/htpasswd;
}

表：内部访问指令

| 名称 | 内部访问指令 |
| 指令 | internal |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 限定 location 的访问路径来源为内部访问请求，否则返回响应状态码 404 |

1) Nginx 限定以下几种类型为内部访问。

*   由 error_page 指令、index 指令、random_index 指令和 try_files 指令发起的重定向请求；
*   响应头中由属性 X-Accel-Redirect 发起的重定向请求，等同于 X-sendfile，常用于下载文件控制的场景中；
*   ngx_http_ssi_module 模块的 include virtual 指令、ngx_http_addition_module 模块、auth_request 和 mirror 指令的子请求；
*   用 rewrite 指令对 URL 进行重写的请求。

2) 内部请求的最大访问次数是 10 次，以防错误配置引发内部循环请求，超过限定次数将返回响应状态码 500。

配置样例如下：

error_page 404 /404.html;

location = /404.html {
    internal;
}

表：响应限速指令

| 名称 | 响应限速指令 |
| 指令 | limit_rate |
| 作用域 | http, server, location |
| 默认值 | 0 |
| 指令说明 | 服务端响应请求后，被限定传输速率的大小。速率是以字节/秒为单位指定的，0 值表示禁用速率限制 |

配置样例如下：

server {
    location /flv/ {
        flv; limit_rate_after 500k;         # 当传输速率到 500KB/s 时执行限速
        limit_rate 50k;                     # 限速速率为 50KB/s
    }
}

响应速率也可以在 proxy_pass 的响应头属性 X-Accel-Limit-Rate 字段中设定；可以通过 proxy_ignore_headers、fastcgi_ignore_headers、uwsgi_ignore_headers 和 scgi_ignore_headers 指令禁用此项功能；在 Nginx 1.17.0 以后的版本中，参数值可以是变量。

map $slow $rate {
    1     4k;
    2     8k;
}

limit_rate $rate;

表：响应最大值后限速指令

| 名称 | 响应最大值后限速指令 |
| 指令 | limit_rate_after |
| 作用域 | http, server, location |
| 默认值 | 0 |
| 指令说明 | 服务端响应请求后，当向客户端的传输速率达到指定值时，按照响应限速指令进行限速 |

配置样例如下：

location /flv/ {
    flv;
    limit_rate_after 500k;
    limit_rate       50k;
}