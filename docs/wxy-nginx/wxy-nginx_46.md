# Nginx WebDAV 模块配置简述

> 原文：[`www.weixueyuan.net/a/738.html`](http://www.weixueyuan.net/a/738.html)

WebDAV（Web-based Distributed Authoring and Versioning）是基于 HTTP/1.1 的增强协议。该协议使用户可以直接对 Web 服务器进行文件读写，并支持对文件的版本控制和写文件的加锁及解锁等操作。

Nginx 通过 ngx_http_dav_module 模块实现对 WebDAV 协议的支持，使用户通过 WebDAV 模块的配置指令实现文件的管理操作，该模块支持 WebDAV 协议的 PUT、DELETE、MKCOL、COPY 和 MOVE 请求方法，在配置编译参数时，需要添加 --with-http_dav_module 参数启用该功能。

ngx_http_dav_module 模块的配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| create_full_put_path | on 或 off | off | 启用创建目录支持，默认情况下，Put 方法只能在已存在的目录里创建文件 |
| dav_access | users:permissions... | user:rw | 设置创建的文件及目录的访问权限，如果定义了 group 或 all 权限，user 设置可省略 |
| dav_methods | off 或 [PUT][DELETE][MKCOL][COPY][MOVE] | off | 指定支持的 WebDAV 方法 |
| min_delete_depth | number | 0 | 允许删除文件及目录的最小层级，小于该层级的文件及目录不允许删除 |

上述指令都可编写在 http、server、location 指令域中。

Nginx 的自有模块对 WebDAV 协议的支持并不完整，可以通过第三方模块 nginx-dav-ext-module 增加文件特性查找和对写文件的加锁与解锁支持。nginx-dav-ext-module 模块的配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| dav_ext_methods | [PROPFIND][OPTIONS][LOCK][UNLOCK] | -- | 指定支持的 WebDAV 方法 |
| dav_ext_lock_zone | zone=NAME:SIZE[timeout=TIMEOUT] | -- | 定义存储文件锁的共享内存区域及锁超时时间，默认锁超时时间是 1 分钟 |
| dav_ext_lock | zone=NAME | -- | 启用 WebDav 的锁操作支持 |

关于上表，有以下几点需要说明。

*   dav_ext_lock_zone 指令只能编写在 http 指令域中；
*   dav_methods 和 dav_ext_lock 指令可编写在 http、server、location 指令域中；
*   WebDAV 协议方法及方法说明如下表所示。

| 方法名称 | 文件权限 | 方法说明 |
| OPTIONS | -- | 支持 WebDAV 的检索服务方法 |
| GET | 读 | 获取文件 |
| PUT、POST | 写 | 上传文件 |
| DELETE | 删除 | 删除文件或集合 |
| COPY | 读、写 | 复制文件 |
| MOVE | 删除、写 | 移动文件 |
| MKCOL | 写 | 创建由一个或多个文件 URI 组成的新集合 |
| PROPFIND | 读 | 获取一个或多个文件的特性（创建日期、文件作者等），实现文件的查找与管理 |
| LOCK、UNLOCK | 写 | 添加、删除文件锁，实现写操作保护 |

进行 WebDAV 协议的 MOVE/COPY 操作时，会通过 HTTP 请求头属性字段 Destination 指定目标路径，如果客户端请求头中没有字段 Destination，Nginx 会直接报错。为增加服务端兼容性，可以通过第三方模块 headers-more-nginx-module 的 more_set_input_headers 指令在 MOVE/COPY 操作的 HTTP 请求头中强制添加 Destination 字段。

WebDAV 协议服务配置过程如下所示。

#### 1) 模块编译

模块编译配置样例如下：

# 编译模块
$ ./configure --with-http_dav_module --add-module=../nginx-dav-ext-module --add-module=../headers-more-nginx-module

#### 2) 设置文件夹权限

文件夹权限配置样例如下：

chown -R nobody:nobody /opt/nginx-web/davfile
chmod -R 700 /opt/nginx-web/davfile

#### 3) 设置登录账号及密码

登录账号及密码配置样例如下：

echo "admin:$(openssl passwd 123456)" >/etc/nginx/conf/.davpasswd

#### 4) Nginx 配置

Nginx 配置样例如下：

```

dav_ext_lock_zone zone=davlock:10m;                   # DAV 文件锁内存共享区

server {
    listen 443 ssl http2;                             # 启用 HTTPS 及 HTTP/2 提升传输性能
    server_name  dav.nginxbar.org;
    access_log  logs/webdav.access.log  main;
    root    /opt/nginx-web/davfile;

    ssl_certificate ssl/www_nginxbar_org.pem;         # 网站证书文件
    ssl_certificate_key ssl/www_nginxbar_org.key;     # 网站证书密钥文件
    ssl_password_file ssl/www_nginxbar_org.pass;      # 网站证书密钥密码文件
    ssl_session_cache shared:SSL:10m;                 # 会话缓存存储大小为 10MB
    ssl_session_timeout  20m;                         # 会话缓存超时时间为 20 分钟

    client_max_body_size 20G;                         # 最大允许上传的文件大小

    location / {
        autoindex on;
        autoindex_localtime on;

        set $dest $http_destination;
        if (-d $request_filename) {                   # 对目录请求、对 URI 自动添加“/”
            rewrite ^(.*[^/])$ $1/;
            set $dest $dest/;
        }

        if ($request_method ~ (MOVE|COPY)) { # 对 MOVE|COPY 方法强制添加 Destination 请求头
            more_set_input_headers 'Destination: $dest';
        }

        if ($request_method ~ MKCOL) {
            rewrite ^(.*[^/])$ $1/ break;
        }

        dav_methods PUT DELETE MKCOL COPY MOVE;      # DAV 支持的请求方法
        dav_ext_methods PROPFIND OPTIONS LOCK UNLOCK;# DAV 扩展支持的请求方法
        dav_ext_lock zone=davlock;                   # DAV 扩展锁绑定的内存区域
        create_full_put_path  on;                    # 启用创建目录支持
        dav_access user:rw group:r all:r;            # 设置创建的文件及目录的访问权限

        auth_basic "Authorized Users WebDAV";
        auth_basic_user_file /etc/nginx/conf/.davpasswd;
    }
}
```

主流操作系统均支持 WebDAV 协议，用户既可以直接通过添加网络设备的方式添加 WebDAV 网站目录，也可以使用支持 WebDAV 协议的客户端进行访问。