# Nginx referer：请求头控制模块

> 原文：[`www.weixueyuan.net/a/679.html`](http://www.weixueyuan.net/a/679.html)

referer 请求头控制模块可以通过设置请求头中的属性字段 Referer 的值控制访问的拒绝与允许。Referer 字段用来表示当前请求的跳转来源，由于该字段可能会涉及隐私权问题，部分浏览器允许用户不发送该属性字段，因此也会存在浏览器正常的请求头中无 Referer 字段的情况。

另外，有些代理服务器或防火墙也会把 Referer 字段过滤掉。通常情况下，伪造 Referer 字段的内容是很容易的，因此该模块主要用于浏览器正常发送请求中 Referer 值的过滤。

虽然通过 Referer 字段进行来源控制并不十分可靠，但用在防盗链的场景中还是基本可以满足需求的。该模块的内置配置指令如下表所示。

| 指令 | 作用域 | 默认值  | 指令说明 |
| referer_hash_max_size | server, location  | 2048 | referer 指令中，存储变量的哈希表的大小 |
| referer_hash_bucket_size | server, location | 64  | referer 指令中，存储变量的哈希桶的大小 |
| valid_referers | server, location | -- | 当用户的 HTTP 头的属性字段 Referer 的值符合指令值的检测时，设置变量 $invalid_referer 为空 |

referer 指令值参数如下表所示。

| 参数名称 | 参数说明 |
| none | Referer 的值为空 |
| blocked | 代理服务器或防火墙过滤后的 Referer 值，这些值都不以 http:// 或 https:// 开头 |
| server_names | Referer 的值中包含一个服务器名 |

配置样例如下：

server{
    listen 8080;
    server_name nginxtest.org;
    root /opt/nginx-web/www;
    valid_referers none blocked *.nginxtest.org;
         # 当 Referer 为空或内容不包含“http://”或以“https://”开头的主机名为“*.nginxtest.
        # org”时允许访问
    if ($invalid_referer) {
        return 403;
    }
}

指令值为字符串时，既可以是包含前缀或后缀的主机名，也可以是包含主机名的 URI。

指令值为正则表达式时，必须以 `~` 开头，Nginx 将从“http://”或“https://”之后的字符串开始匹配。

默认变量 $invalid_referer 的值为 1，当 Referer 的值与指令值的内容匹配时，$invalid_referer 的值为空。