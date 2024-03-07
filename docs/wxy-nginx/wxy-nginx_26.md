# Nginx allow、deny：IP 访问控制模块

> 原文：[`www.weixueyuan.net/a/682.html`](http://www.weixueyuan.net/a/682.html)

Nginx 中 IP 访问控制模块名称为 ngx_http_access_module，该模块可以对客户端的源 IP 地址进行允许或拒绝访问控制。该模块的内置配置指令如下面表格中所示。

表：允许访问指令

| 名称 | 允许访问指令 |
| 指令  | allow |
| 作用域 | http、server、location、limit_except |
| 默认值 | -- |
| 指令说明 | 允许指定源 IP 的客户端请求访问 |

表：拒绝访问指令

| 名称 | 拒绝访问指令 |
| 指令 | deny |
| 作用域 | http、server、location、limit_except |
| 默认值 | -- |
| 指令说明 | 拒绝指定源 IP 的客户端请求访问 |

配置样例如下：

location / {
    deny  192.168.1.1;          # 禁止 192.168.1.1
    allow 192.168.0.0/24;       # 允许 192.168.0.0/24 的 IP 访问
    allow 10.1.1.0/16;          # 允许 10.1.1.0/16 的 IP 访问
    allow 2001:0db8::/32;
    deny  all;
}

Nginx 会按照自上而下的顺序进行匹配。