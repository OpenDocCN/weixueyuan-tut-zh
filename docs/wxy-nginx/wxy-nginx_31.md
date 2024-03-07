# Nginx expires：页面缓存时间配置

> 原文：[`www.weixueyuan.net/a/699.html`](http://www.weixueyuan.net/a/699.html)

Nginx 缓存的设置可以提高网站性能，对于网站的图片，尤其是新闻网站，图片一旦发布，改动的可能是非常小的，为了减小对服务器请求的压力，提高用户浏览速度，我们可以通过设置 Nginx 中的 expires 指令，让用户访问一次后，将图片缓存在用户的浏览器中。

需要注意的是，这种缓存方式只能在用户不对浏览器强制刷新的情况下生效，如果用户通过 url 来进行访问，是可以访问到缓存的。

Nginx 中的 expires 指令的介绍如下表所示：

| 名称 | 缓存时间指令 |
| 指令 | expires |
| 作用域 | http、server、location、if in location |
| 默认值 | off |
| 指令值可选项 | 时间或 epoch 或 max 或 off |
| 指令说明 | 当响应状态码为 200、201、204、206、301、302、303、304、307 或 308 时，对响应头中的属性字段“Expires”和“Cache-Control”进行添加或编辑操作 |

当指令值为时间时，既可以是正值也可以是负值。Expires 的值为当前时间与指令值的时间之和。当指令值的时间为正或 0 时，Cache-Control 的值为指令值的时间。当指令值的时间为负时，Cache-Control 的值为 no-cache。

当指令值为时间时，可用前缀 `@` 指定一个绝对时间，表示在当天的指定时间失效。

当指令值为 epoch 时，Expires 的值为 Thu,01 Jan 1970 00:00:01 GMT，Cache-Control 的值为 no-cache。

当指令值为 max 时，Expires 的值为 Thu,31 Dec 2037 23:55:55 GMT，Cache-Control 的值为 10 年。

当指令值为 off 时，不对响应头中的属性字段 Expires 和 Cache-Control 进行任何操作。

配置样例如下：

map $content_type $expires {          # 根据$content_type 的值，对变量$expires 进行赋值
    default         off;                            # 默认不修改 Expires 和 Cache-Control 的值
    application/pdf 42d;                     # application/pdf 类型为 42 天
    ~image/         max;                       # 图片类型为 max
}

server {
    expires    24h;                             # 设置 Expires 的值为当前时间之后的 24 小时，
                         　　                        # Cache-Control 的值为 24 小时
    expires    modified +24h;           # 编辑 Expires 的值增加 24 小时，Cache-Control 的值增
                                                       # 加 24 小时
    expires    @15h;                         # 设置 Expires 的值为当前日的 15 点，Cache-Control 的值
                                                       # 为当前时间到当前日 15 点的时间差
    expires    $expires;                     # 根据变量$expires 的内容设置缓存时间
    add_header Cache-Control no-cache; 
    add_trailer  X-Always $host always;
}