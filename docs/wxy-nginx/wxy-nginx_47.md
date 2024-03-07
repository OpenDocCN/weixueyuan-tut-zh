# Nginx 代理服务器

> 原文：[`www.weixueyuan.net/nginx/proxy_server/`](http://www.weixueyuan.net/nginx/proxy_server/)

Nginx 不仅可以搭建 Web 服务器对外提供内容服务，还可以实现对客户端访问的代理功能。

代理是客户端请求数据处理的中间角色，它本身并不产生响应数据，只是将客户端的请求转发给目标应用服务器，然后目标应用服务器再将响应数据通过代理返回客户端。Nginx 不仅可以实现 HTTP 协议的代理，还支持 TCP/UDP 及基于 HTTP/2 的 gRPC 代理。