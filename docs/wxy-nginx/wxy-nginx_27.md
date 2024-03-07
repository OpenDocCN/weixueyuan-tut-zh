# Nginx 用户 cookie 模块

> 原文：[`www.weixueyuan.net/a/684.html`](http://www.weixueyuan.net/a/684.html)

用户 cookie 模块（ngx_http_userid_module）的作用是为客户端设置 cookie 以标识不同的访问用户。可以通过内部变量 $uid_got 和 $uid_set 记录已接收和设置的 cookie。该模块的内置配置指令如下面表格中所示。

表：用户 cookie 指令

| 名称 | 用户 cookie 指令 |
| 指令 | userid |
| 作用域 | http、server、location |
| 默认值 | off |
| 指令值可选项 | on、off、v1 或 log |
| 指令说明 | 设置关闭或启用用户 cookie 及启用的方式 |

当指令值为 off 时，关闭用户 cookie 接收和记录功能。

当指令值为 on 时，启用用户 cookie 接收和记录功能，默认为 v2 版本设置 cookie。设置 cookie 的响应头标识为 Set-Cookie2。

当指令值为 v1 时，使用 v1 版本设置 cookie，设置 cookie 的响应头标识为 Set-Cookie。

当指令值为 log 时，不设置用户 cookie，但对接收到的 cookie 进行记录。

表：用户 cookie 域指令

| 名称 | 用户 cookie 域指令 |
| 指令 | userid_domain |
| 作用域 | http, server, location |
| 默认值 | none |
| 指令说明 | 设置用户 cookie 中的域名，none 表示禁用 cookie 的域设置 |

表：用户 cookie 过期指令

| 名称 | 用户 cookie 过期指令 |
| 指令 | userid_expires |
| 作用域 | http, server, location |
| 默认值 | off |
| 指令值可选项 | time 或 max 或 off |
| 指令说明 | 设置用户 cookie 的过期时间，time 表示客户端保存 cookie 的时间，max 表示 cookie 的过期时间，默认为会话结束即过期 |

表：用户 cookie 标识指令

| 名称 | 用户 cookie 标识指令 |
| 指令 | userid_mark |
| 作用域 | http, server, location |
| 默认值 | off |
| 指令值可选项 | letter 或 digit 或 = 或 off |
| 指令说明 | 设置用户 cookie 的标识机制并设置用作标记的字符。该标识机制用于在保存客户标识符的同时添加或修改 userid_p3p 及 cookie 的过期时间 |

用作标记的指令值可以是任意英文字母（区分大小写）、数字或“=”。

userid_mark 设置完成后，将与用户 cookie 中传送的 Base64 格式的标识的第一个字符进行比较，如果不匹配，则重新发送用户标识、userid_p3p 及 cookie 的过期时间。

表：用户 cookie 名称指令

| 名称 | 用户 cookie 名称指令 |
| 指令 | userid_name |
| 作用域 | http, server, location |
| 默认值 | uid |
| 指令说明 | 设置 cookie 名称 |

表：用户 p3p 指令

| 名称 | 用户 p3p 指令 |
| 指令 | userid_p3p |
| 作用域 | http、server、location |
| 默认值 | none |
| 指令说明 | 设置是否将 p3p 头属性字段同 cookie 一同发送 |

P3P 是 W3C 推荐的隐私保护标准，P3P 头属性字段通常用于解决与支持 P3P 协议的浏览器的跨域访问问题。

表：用户 cookie 路径指令

| 名称 | 用户 cookie 路径指令 |
| 指令 | userid_path |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 设置 cookie 路径 |

表：用户 cookie 源服务器指令

| 名称  | 用户 cookie 源服务器指令 |
| 指令 | userid_service |
| 作用域 | http, server, location |
| 默认值 | -- |
| 指令说明 | 设置 cookie 的发布服务器。当 cookie 标识符由多个服务器发出时，为确保用户标识的唯一性，则应为每个服务器分配编号，cookie 版本 1 时默认为 0，cookie 版本 2 时默认为服务器 IP 地址的最后 4 个八位字节组成的数字 |

配置样例如下：

server {
    listen 8083;
    server_name example.com;
    root /opt/nginx-web;

    auth_request /auth;

    userid         on;
    userid_name    uid;
    userid_domain  example.com;
    userid_path    /;
    userid_expires 1d;
    userid_p3p     'policyref="/w3c/p3p.xml", CP="CUR ADM OUR NOR STA NID"';

    location / {
        index  index.html index.htm;
        add_header    Set-Cookie "username=$remote_user";
    }
    location /auth {
        proxy_pass_request_body off;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-URI $request_uri;
        proxy_pass http://192.168.2.145:8080/HttpBasicAuth.php;
    }
}