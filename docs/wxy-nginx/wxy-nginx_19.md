# Nginx alias 指令：虚拟目录配置

> 原文：[`www.weixueyuan.net/a/664.html`](http://www.weixueyuan.net/a/664.html)

Nginx 中想要配置虚拟目录可以使用 alias 指令，该指令的介绍如下表所示：

| 名 称 | 访问路径别名指令 |
| 指令 | alias |
| 作用域 | location |
| 默认值 | -- |
| 指令说明 | 默认情况下，本地文件的路径是 root 指令设定根目录的相对路径，通过 alias 指令可以将匹配的访问路径重新指定为新定义的文件路径。 |

配置样例如下：

server{
    listen 8080;
    server_name www.nginxtest.org;
    root /opt/nginx-web/www;
    location /flv/ {
        alias /opt/nginx-web/flv/;
    }

    location /js {
        alias /opt/nginx-web/js;
    }

    location /img {
        alias /opt/nginx-web/img/;
    }
}

可以用如下命令进行访问测试：

curl http://127.0.0.1:8080/flv/
curl -L http://127.0.0.1:8080/js
curl http://127.0.0.1:8080/js/
curl -L http://127.0.0.1:8080/img
curl http://127.0.0.1:8080/img/

alias 指定的目录是 location 路径的实际目录，其所在 location 的 rewrite 指令不能使用 break 参数。