# Nginx root 指令：根目录配置

> 原文：[`www.weixueyuan.net/a/662.html`](http://www.weixueyuan.net/a/662.html)

用户请求的最终结果是要返回数据，当响应文件在 Nginx 服务器本地时，需要进行本地文件位置、读或写、返回执行结果的操作。Nginx 中的 root 指令可以设定请求 URL 的本地文件根目录，如下表所示。

| 名称 | 根目录指令 |
| 指令 | root |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令说明 | 设定请求 URL 的本地文件根目录 |

配置样例如下：

location /flv/ {
    root /data/web;
}

当 root 指令在 location 指令域时，root 设置的是 location 匹配访问路径的上一层目录，样例中被请求文件的实际本地路径为 /data/web/flv/。

location 中的路径是否带“/”，对本地路径的访问无任何影响。