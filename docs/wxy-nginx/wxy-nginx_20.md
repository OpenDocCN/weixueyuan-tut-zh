# Nginx 配置项 try_files 简介

> 原文：[`www.weixueyuan.net/a/666.html`](http://www.weixueyuan.net/a/666.html)

try_files 指令是在 Nginx0.7.27 版本中开始加入的，它可以按顺序检查文件是否存在，并返回第一个找到的文件，如果未找到任何文件，则会调用最后一个参数进行内部重定向，如下表所示：

| 名称 | 文件判断指令 |
| 指令 | try_files |
| 作用域 | server、location |
| 默认值 | -- |
| 指令说明 | 用于顺序检查指定文件是否存在，如果不存在，则按照最后一个指定 URI 做内部跳转 |

配置样例如下：

location /images/ {
    # $uri 存在则执行代理的上游服务器操作，否则跳转到 default.gif 的 location
    try_files $uri /images/default.gif;
}

location = /images/default.gif {
    expires 30s;
}

跳转的目标也可以是一个 location 区域，脚本如下：

http{
    location / {
        try_files /system/maintenance.html $uri $uri/index.html $uri.html @mongrel;
    }
    location @mongrel {
        proxy_pass http://mongrel; 
    }
}