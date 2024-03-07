# Nginx server_name：配置主机名称

> 原文：[`www.weixueyuan.net/a/638.html`](http://www.weixueyuan.net/a/638.html)

Nginx 中的 server_name 指令主要用于配置基于名称的虚拟主机，其说明如下表所示：

| 名称 | 主机名指令 |
| 指令 | server_name |
| 作用域 | server |
| 默认值 | -- |
| 指令说明 | 设定所在 server 指令域的主机名 |

配置样例如下所示：

http {
    server {
        server_name example.com .example.com;       # 泛域名的使用
        server_name www.example.;                   # 多个后缀域名的使用 server_name
        www.example.com ~^www.example.com$;         # 正则表达式匹配
        # 正则匹配变量的场景
        server_name ~^(www\.)?(.+)$;
        location / {
            root /sites/$2;
        }

        # 正则匹配为变量的场景
        server_name ~^(www\.)?(?<domain>.+)$;
        location / {
            root /sites/$domain;
        }
    }
}

当 server_name 指令值中有多个主机名时，第一个主机名为首主机名。