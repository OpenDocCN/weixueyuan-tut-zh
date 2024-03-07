# Nginx expires、etag、if_modified_since：客户端缓存控制

> 原文：[`www.weixueyuan.net/a/770.html`](http://www.weixueyuan.net/a/770.html)

客户端的缓存有两种验证机制，一种是基于有效期的本地有效期验证；另一种是由服务端提供的服务端验证。Nginx 提供了 expires、etag、if_modified_since 指令可实现对客户端缓存的控制。

## 1、有效期验证

expires 指令可实现在响应状态码为 200、201、204、206、301、302、303、304、307 或 308 时，对响应头中的属性字段 Expires 和 Cache-Control 进行添加或编辑操作。该指令会同时设置 Expires 和 Cache-Control 两个字段，客户端根据这两个字段的值执行内容缓存的本地有效期设置。

#### 1) 设置相对时间

响应头字段 Expires 的值为当前时间与指令值的时间之和，响应头字段 Cache-Control 的值为指令值的时间。

```

server {
    expires    24h;           # 设置 Expires 为当前时间过后的 24 小时，Cache-Control 的值为 24
                              # 小时
    expires    modified +24h; # 编辑 Expires 增加 24 小时，Cache-Control 的值增加 24 小时
    expires    $expires;      # 根据变量$expires 的内容设置缓存时间
}
```

#### 2) 设置绝对时间

可以通过前缀 @ 指定一个绝对时间，表示在当天的指定时间失效。

```

server {
    expires    @15h;        # 设置 Expires 为当前日的 15 点，Cache-Control 的值为到
                            # 当前时间到 15 点的时间差
}
```

#### 3) 无有效期设置

时间为负值或为 epoch 时，响应头字段 Cache-Control 的值为 no-cache，表示当前响应数据的内容缓存无有效期。

```

server {
    expires    -1;
    expires    epoch;
}
```

#### 4) 最大值设置

指令值为 max 时，Expires 的值为 Thu,31 Dec 2037 23:55:55 GMT，Cache-Control 为 10 年。

```

server {
    expires  max;
}
```

Nginx 除了提供指令 expires 可以实现有效期控制外，还提供了指令 add_header，可以让用户自定义响应头实现客户端缓存的控制。

```

server {
    add_header Cache-Control no-cache;  # 响应数据的内容缓存无有效期
}
```

## 2、服务端验证

#### 1) Etag 实体标签

Nginx 作为 Web 服务器时，对静态资源会自动在响应头中添加响应头字段 Etag，字段值为静态资源文件的最后编辑时间（last_modified_time）和文件大小的十六进制组合。对于代理的响应内容则由被代理服务器进行控制，不会自动添加 Etag 字段，只有存在 Nginx 服务器由 Nginx 直接读取的文件时才会自动添加 Etag 字段，它可以通过添加 etag off 指令禁止自动生成 Etag。

#### 2) 文件修改时间

Nginx 作为 Web 服务器时，会对静态资源自动添加响应头字段 Last-Modified，字段值为静态资源文件的最后编辑时间（last_modified_time）。

Nginx 提供了配置指令 if_modified_since，对文件修改时间的服务端校验提供了两种不同的比对方式。一种是指令值为 exact 时，Nginx 会将请求头中 if_modified_since 的时间与响应数据中的时间做精确匹配，即完全相等才认为客户端缓存有效，返回响应状态码 304；另一种是指令值为 before 时，则在请求头中 if_modified_since 的时间大于响应数据中的时间也认为客户端缓存有效，返回响应状态码 304。该指令功能控制处于数据流的出入口，对于任何形式产生的响应数据都有效，当指令值为 off 时，则关闭 Nginx 对客户端缓存文件修改时间的服务端校验功能。

任何与用户私人相关的数据都不应该被缓存，所以对于私人内容数据建议设置 HTTP 信息头 Cache-Control 字段值为 no-cache、no-store 或 private 控制客户端不进行缓存，根据数据内容的敏感性，正确设置这些头字段，可以在保持维护私人信息安全的前提下利用缓存的优势提升网站的响应速度。