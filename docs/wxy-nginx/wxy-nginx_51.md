# Nginx 基于 SSL 的 TCP 代理服务器

> 原文：[`www.weixueyuan.net/a/755.html`](http://www.weixueyuan.net/a/755.html)

Nginx 可以通过代理模块实现上游服务器 SSL/TLS 协议的连接，同时 Nginx 还通过模块 ngx_stream_ssl_module 提供了基于 SSL/TLS 协议的 TCP 连接监听。Nginx 还可以把 SSL 证书部署在 Nginx 服务器上，这就减轻了后端上游服务器的 CPU 运算量并实现 SSL 证书的统一管理和维护。

ngx_stream_ssl_module 模块默认不会被构建，这就需要在编译的时候通过 `--with-stream_ssl_module` 参数进行启用。相关配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| ssl_protocols | [SSLv2][SSLv3] [TLSv1][TLSv1.1]
[TLSv1.2][TLSv1.3] | TLSv1 TLSv1.1 TLSv1.2 | 设置使用的 SSL 协议版本 |
| ssl_certificate | file | -- | PEM 格式的 SSL 证书文件，可自建或由 CA 机构颁发 |
| ssl_certificate_key | file | -- | PEM 格式的 SSL 证书私钥文件，可自建或由 CA 机构颁发 |
| ssl_password_file | file | -- | 存放 SSL 证书私钥文件的密码文件，一个密码一行。有多个密码时，Nginx 会依次尝试 |
| ssl_ciphers | ciphers | HIGH:!aNULL:!MD5 | 设置 SSL TCP 建立连接时用于协商使用的加密算法组合，也称为密码套件。指令值内容为 openssl 的密码套件名称，多个套件名称由“:”分隔 |
| ssl_prefer_server_ciphers | on 或 off | off | 是否启用 SSLv3 和 TLSv1 协议在 SSL TCP 连接时优先使用服务端设置的密码套件 |
| ssl_dhparam | file | -- | DH 密钥交换的 Diffie-Hellman 参数文件 |
| ssl_ecdh_curve | curve | auto | 配置 SSL 加密时使用椭圆曲线 DH 密钥交换的曲线参数，多个参数使用“:”分隔。ecdh 是 Elliptic-Curve 和 Diffie-Hellman 的缩写，指令值为 auto 时，配置的曲线参数是 prime256v1 |
| ssl_session_cache | off 或 none 或 [builtin[:size]]
[shared:name:size] | none | SSL TCP 会话缓存设置 |
| ssl_session_tickets | on 或 off | on | 是否启用 SSL TCP 会话缓存 session ticket 机制，指令值为 off 时，使用 session ID 会话缓存机制 |
| ssl_session_ticket_key | file | -- | 指定会话凭证密钥文件，用以多台 Nginx 间实现 session ticket 共享，否则 Nginx 会随机生成一个会话凭证密钥 |
| ssl_session_timeout | time | 5m | 设置客户端可用会话缓存的超时时间 |
| ssl_verify_client | on 或 off 或 optional 或
optional_no_ca | off | 设置是否启用对客户端证书验证功能，指令值为 on 时，启用验证；指令值为 optional 时，如果接收到客户端证书则启用验证；指令值为 optional_no_ca 时，若接收到客户端证书，则启用客户端证书验证，但不进行证书链校验。验证结果将存储在 $ssl_client_verity 变量中 |
| ssl_crl | file | -- | 证书吊销列表文件，用以验证客户端 SSL 证书有效性的 PEM 格式文件 |
| ssl_client_certificate | file | -- | 指定一个 PEM 格式的 CA 证书（根或中间证书）文件，该证书用作客户端的证书验证。该证书列表会被发送给客户端 |
| ssl_trusted_certificate | file | -- | 指定一个 PEM 格式的 CA 证书（根或中间证书）文件，该证书用作客户端的证书验证。该证书列表不会被发送给客户端 |
| ssl_verify _depth | number | 1 | 设置客户端证书链验证深度 |

关于上表有以下几点需要说明。

*   该模块指令值使用的指令域范围为 stream、server。
*   Nginx 建立 SSL TCP 监听，用户发送 SSL TCP 连接时，由 Nginx 实现 SSL 终止并把 TCP 会话代理到上游服务器，部署方式为客户端 → Nginx 服务器（SSL TCP）→ 上游服务器（TCP）。配置样例如下：

```

stream {
    server {
        listen              636 ssl;                    # 设置监听端口为 636
        access_log logs/ldap_access.log tcp;

        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;      # 设置使用的 SSL 协议版本
        ssl_ciphers         AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
            # 设置服务端使用的密码套件
        ssl_certificate     ssl/www_nginxbar_org.pem;   # 主机名 www.nginxbar.org 证书文件
        ssl_certificate_key ssl/www_nginxbar_org.key;   # 主机名 www.nginxbar.org 证书密钥文件
        ssl_session_cache   shared:SSL:10m;             # SSL TCP 会话缓存设置共享内存区域名为
                                                        # SSL，区域大小为 10MB
        ssl_session_timeout 10m;                        # SSL TCP 会话缓存超时时间为 10 分钟
        proxy_pass                    192.168.2.100:389;
    }
}
```

*   也可以通过代理模块的 proxy_ssl 指令配置与上游服务器实现全链路的安全数据通信。部署方式为客户端 → Nginx 服务器（SSL TCP）→ 被代理服务器（SSL TCP）。配置样例如下：

```

stream {
    server {
        listen              636 ssl;                   # 设置监听端口为 636
        access_log logs/ldap_access.log tcp;

        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;     # 设置使用的 SSL 协议版本
        ssl_ciphers         AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
            # 设置服务端使用的密码套件
        ssl_certificate     ssl/www_nginxbar_org.pem;  # 主机名 www.nginxbar.org 证书文件
        ssl_certificate_key ssl/www_nginxbar_org.key;  # 主机名 www.nginxbar.org 证书密钥文件
        ssl_session_cache   shared:SSL:10m;  # SSL TCP 会话缓存设置共享内存区域名为 SSL，区域大小为 10MB
        ssl_session_timeout 10m;             # SSL TCP 会话缓存超时时间为 10 分钟

        proxy_ssl   on;                      # 启用 SSL/TLS 协议，与被代理服务器建立连接
        proxy_ssl_session_reuse on;          # 与被代理服务器 SSL TCP 连接的 SSL 会话重用功能
    }
}
```