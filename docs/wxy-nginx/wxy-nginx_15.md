# Nginx localhost 路由匹配规则

> 原文：[`www.weixueyuan.net/a/650.html`](http://www.weixueyuan.net/a/650.html)

URI 即统一标识资源符，通用的 URI 语法格式如下：

scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]

格式说明如下：

*   在 Nginx 的应用场景中，URL 与 URI 并无明确区别。URI 标准（RFC3986）中约定，URL 是 URI 的一个子集；
*   scheme 是 URI 请求时遵守的协议，常见的有 HTTP、HTTPS、FTP；
*   host[:port] 是主机名与端口号，HTTP 协议的默认端口是 80，HTTPS 协议的默认端口是 443；
*   [/path] 是访问路径与访问文件名；
*   [?query] 是访问参数，访问参数以“?”开始作标识，由多个以“&”连接的 key=value 形式的字符串组成。

## 1、URI 匹配规则

location 是 Nginx 对 HTTP 请求中的 URI 进行匹配处理的指令，location 的语法形式如下：

location [=|~|~*|^~|@] pattern { ... }

其中，`[=|~*|^~|@]`部分称为 location 修饰语（Modifier），修饰语定义了与 URI 的匹配方式。pattern 为匹配项，可以是字符串或正则表达式。

无修饰语：完全匹配 URI 中除访问参数以外的内容，匹配项的内容只能是字符串，不能是正则表达式。

location /images {
    root /data/web;
}

修饰语“=”：完全匹配 URI 中除访问参数以外的内容，Linux 系统下会区分大小写，Windows 系统下则不会。

location = /images {
    root /data/web;
}

修饰语“~”：完全匹配 URI 中除访问参数以外的内容，Linux 系统下会区分大小写，Windows 系统下则会无效。匹配项的内容必须是正则表达式。

location ~ /images/.*\.(gif|jpg|png)$ {
    root /data/web;
}

修饰语“~*”：完全匹配 URI 中除访问参数以外的内容，不区分大小写。匹配项的内容必须是正则表达式。

location ~* \.(gif|jpg|png)$ {
    root /data/web;
}

修饰语“^~”：完全匹配 URI 中除访问参数以外的内容，匹配项的内容如果不是正则表达式，则不再进行正则表达式测试。

location ^~ /images {
    root /data/web;
}

修饰语“@”：定义一个只能内部访问的 location 区域，可以被其他内部跳转指令使用，如 try_files 或 error_page。

location @images { 
    proxy_pass http://images; 
}

## 2、匹配顺序

1) 先检测匹配项的内容为非正则表达式修饰语的 location，然后再检测匹配项的内容为正则表达式修饰语的 location。

2) 匹配项的内容为正则与非正则都匹配的 location，按照匹配项的内容为正则匹配的 location 执行。

3) 所有匹配项的内容均为非正则表达式的 location，按照匹配项的内容完全匹配的内容长短进行匹配，即匹配内容多的 location 被执行。

4) 所有匹配项的内容均为正则表达式的 location，按照书写的先后顺序进行匹配，匹配后就执行，不再做后续检测。

## 3、其他事项

当 location 为正则匹配且内部有 proxy_pass 指令时，proxy_pass 的指令值中不能包含无变量的字符串。修饰语“^~”不受该规则限制。

location ~ /images { 
    proxy_pass http://127.0.0.1:8080;                                 # 正确的指令值
    proxy_pass http://127.0.0.1:8080$request_uri;             # 正确的指令值
    proxy_pass http://127.0.0.1:8080/image$request_uri; # 正确的指令值
    proxy_pass http://127.0.0.1:8080/;                               # 错误的指令值
}

## 4、访问路由指令

访问路由指令如下面表格所示。

表：合并空斜线指令

| 名称 | 合并空斜线指令 |
| 指令 | merge_slashes |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令值选项 | off 或 on |
| 指令说明 | 当指令值为 on，在访问路径中相邻斜线内容为空时进行合并 |

配置样例如下：

http {
    merge_slashes off;
}

表：跳转主机名指令

| 名称 | 跳转主机名指令 |
| 指令 | server_name_in_redirect |
| 作用域 | http, server, location |
| 默认值 | off |
| 指令说明 | 默认情况下，Nginx 重定向时，会用当前 server 指令域中主机的 IP 与 path 拼接成完整的 URL 进行重定向。开启该参数后，Nginx 会先查看当前指令域中 server_name 的第一个主机名，如果没有，则会查找请求头中 host 字段的内容，如果再没有则会用 IP 与 path 进行拼接 |

配置样例如下：

http {
    server_name_in_redirect on;
}

表：跳转端口指令

| 名称 | 跳转端口指令 |
| 指令 | port_in_redirect |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令说明 | Nginx 重定向时，会用当前 server 指令域的监听端口与主机拼接成完整的 URL 进行重定向。当指令值为 off 时，则默认用 80 端口 |

配置样例如下：

http {
    port_in_redirect on;
}

表：子请求输出缓冲区大小指令

| 名称 | 子请求输出缓冲区大小指令 |
| 指令 | subrequest_output_buffer_size |
| 作用域  | http, server, location |
| 默认值 | 4k 或 8k |
| 指令说明 | 设置用于存储子请求响应报文的缓冲区大小，默认值与操作系统的内存页大小一致 |

配置样例如下：

http {
   subrequest_output_buffer_size 64K;
}

表：绝对跳转指令

| 名称 | 绝对跳转指令 |
| 指令 | absolute_redirect |
| 作用域 | http, server, location |
| 默认值   | on |
| 指令值选项 | off 或 on |
| 指令说明 | Nginx 发起的重定向使用绝对路径做跳转，即用主机名和端口及访问路径的方式，如果关闭的话，则跳转为默认相对当前请求的主机名和端口的访问路径 |

配置样例如下：

http {
    absolute_redirect off;
}

表：响应刷新指令

| 名称  | 响应刷新指令 |
| 指令 | msie_refresh |
| 作用域 | http, server, location |
| 默认值 | on |
| 指令值选项 | on 或 off |
| 指令说明 | Nginx 处理页面跳转或刷新的方式通常是以向客户端返回 3xx 状态码来实现。该指令是当客户端为 msie 时，在返回 HTML 头部添加“<meta http-equiv=\"Refresh\" content=\"0;\" url=*>” |

配置样例如下：

http {
   msie_refresh off;
}