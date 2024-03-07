# Nginx 静态资源服务器搭建

> 原文：[`www.weixueyuan.net/a/703.html`](http://www.weixueyuan.net/a/703.html)

HTML 是一种标记语言，提供 HTML 文件读取是静态服务器最基本的功能，静态服务器的配置样例如下：

```

server {
    listen 8080;
    root /opt/nginx-web/www;                # 存放静态文件的文件目录
    location / {
        index index.html;
    }
    location /js {
        alias /opt/nginx-web/static/js/;    # 存放 JavaScript 文件的文件目录
        index index.html;
    }
}
```

在以上配置中，每个 server 指令域等同于一个虚拟服务器，每个 location 指令域等同于一个虚拟目录。