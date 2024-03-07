# OpenResty 编译安装

> 原文：[`www.weixueyuan.net/a/611.html`](http://www.weixueyuan.net/a/611.html)

OpenResty 是一个基于 Nginx 与 Lua 的高性能 Web 平台，其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。

OpenResty 通过汇聚各种设计精良的 Nginx 模块（主要由 OpenResty 团队自主开发），从而将 Nginx 有效地变成一个强大的通用 Web 应用平台。这样，Web 开发人员和系统工程师可以使用 Lua 脚本语言调动 Nginx 支持的各种 C 以及 Lua 模块，快速构造出足以胜任 10K 乃至 1000K 以上单机并发连接的高性能 Web 应用系统。

OpenResty 的目标是让你的 Web 服务直接跑在 Nginx 服务内部，充分利用 Nginx 的非阻塞 I/O 模型，不仅仅对 HTTP 客户端请求,甚至于对远程后端诸如 MySQL、PostgreSQL、Memcached 以及 Redis 等都进行一致的高性能响应。

## 1、OpenResty 源码获取

OpenResty 当前源码版本是 1.15.8.2，集成的 Nginx 版本是 Nginx 1.15.8 版本。其源码可通过官网直接获取，获取命令如下：

mkdir -p /opt/data/source
cd /opt/data/source
wget https://openresty.org/download/openresty-1.15.8.2.tar.gz
tar zxmf openresty-1.15.8.2.tar.gz

## 2、编译配置参数

OpenResty 是 Nginx 的扩展版，其在编译配置参数上也进行了清晰的区分，分为 Nginx 原有编译配置参数和扩展编译配置参数两部分，扩展编译配置参数如下表所示。

| 编译参数 | 参数说明 |
| --with-debug | 启用调试日志 |
| --with-dtrace-probes | 启用 DTrace USDT 探针 |
| --without-http_echo_module | 不编译可以在 Nginx 的 URL 访问中通过 echo 命令输出字符到用户的浏览器，一般用来调试输出信息，检测 Nginx 的可访问性、配置正确性 |
| --without-http_xss_module | 不编译跨站点脚本支持 |
| --without-http_coolkit_module | 不编译 Nginx 插件模块集合 |
| --without-http_set_misc_module | 不编译 rewrite 指令的扩展 |
| --without-http_form_input_module | 不编译 HTTP 请求扩展 |
| --without-http_encrypted_session_module | 不编译加密解密 Nginx 变量值的模块 |
| --without-http_srcache_module | 不编译缓存增强模块 |
| --without-http_lua_module | 不编译 Lua 脚本支持模块 |
| --without-http_lua_upstream_module | 不编译提供操作 upstream 服务器 Lua API 功能的模块 |
| --without-http_headers_more_module | 不编译 header 返回信息编辑模块 |
| --without-http_array_var_module | 不编译数组类型的 Nginx 变量模块 |
| --without-http_memc_module | 不编译 Memcache 扩展模块 |
| --without-http_redis2_module | 不编译 Redis 2.0 协议支持模块 |
| --without-http_redis_module | 不编译 Redis 缓存的模块 |
| --without-http_rds_ison_module | 不编译 DBD-Stream 格式转 JSON 格式模块 |
| --without-http_rds_csv_module | 不编译 DBD-Stream 格式转 cvs 格式模块 |
| --without-stream_lua_module | 不编译 TCP/UDP 负载 Lua 脚本支持模块 |
| --without-ngx_devel_kit_module | 不编译模块开发库 |
| --with-http_iconv_module | 编译编码字符转换模块 |
| --without-http_ssl_module | 不编译 HTTP SSL 支持模块 |
| --without-stream_ssl_module | 不编译 Stream SSL 支持模块 |
| --with-http_drizzle_module | 编译 MySQL 或 Drizzle 数据库访问模块 |
| --with-http_postgres_module | 编译 PostgreSQL 数据库访问模块 |
| --without-lua_cjson | 不编译快速解析 JSON 的一个 Lua C 模块 |
| --without-lua_tablepool | 不编译基于 LuaJIT 的 Lua 表格资源回收池支持库 |
| --without-lua_redis_parser | 不编译 Redis 数据解析为 Lua 数据结构模块 |
| --without-lua_rds_parser | 不编译 DBD-Stream 格式的数据解析为 Lua 数据结构模块 |
| --without-lua_resty_dns | 不编译 DNS（域名系统）解析器的 Lua 模块 |
| --without-lua_resty_memcached | 不编译 Memcached 客户端模块 |
| --without-lua_resty_redis | 不编译 Redis 客户端模块 |
| --without-lua_resty_mysql | 不编译 MySQL 客户端模块 |
| --without-lua_resty_upload | 不编译上传文件支持模块 |
| --without-lua_resty_upstream_healthcheck | 不编译基于 Lua 的服务器组健康监测模块 |
| --without-lua_resty_string | 不编译字符串实用程序和通用哈希函数的 Lua 库 |
| --without-lua_resty_websocket | 不编译基于 Lua 的非阻塞 WebSocket 服务/客户端 |
| --without-lua_resty_limit_traffic | 不编译 lua-resty-limit-traffic 支持库 |
| --without-lua_resty_lock | 不编译基于 Lua Nginx 模块的共享内存字典的简单非阻塞互斥锁 API |
| --without-lua_resty_lrucache | 不编译 Lua-land LRU 缓存 |
| --without-lua_resty_signal | 不编译 lua-resty-signal 支持库，该支持库用于向 UNIX 进程发送信号 |
| --without-lua_resty_shell | 不编译 lua-resty-shell 支持库，该支持库可以实现通过 Lua 调用 shell 脚本的支持 |
| --without-lua_resty_core | 不编译使用 LuaJIT FFI 实现 Lua Nginx 模块提供的 Lua API |
| --with-luajit | 编译 LuaJIT 2.1 解释器 |
| --without-luajit-lua52 | 不编译基于 Lua 5.2 的 LuaJIT 扩展解释器 |

对于下表，有以下几点说明。

*   扩展的功能模块都是被默认编译的，可以通过参数设置选择不编译；
*   DTrace 是基于系统底层的性能监控技术，可监测函数级别的内存、CPU 性能数据；
*   Nginx 原有编译配置参数参见《Nginx 编译安装》一节。

## 3、代码编译

OpenResty 代码编译如下所示：

yum -y install gcc pcre-devel make zlib-devel openssl-devel libxml2-devel \
    libxslt-devel gd-devel GeoIP-devel libatomic_ops-devel luajit \
    luajit-devel perl-devel perl-ExtUtils-Embed

./configure \
    --with-threads \
    --with-file-aio \
    --with-http_ssl_module \
    --with-http_v2_module \
    --with-http_realip_module \
    --with-http_addition_module \
    --with-http_xslt_module=dynamic \
    --with-http_image_filter_module=dynamic \
    --with-http_geoip_module=dynamic \
    --with-http_sub_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_mp4_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_auth_request_module \
    --with-http_random_index_module \
    --with-http_secure_link_module \
    --with-http_degradation_module \
    --with-http_slice_module \
    --with-http_stub_status_module \
    --with-stream=dynamic \
    --with-stream_ssl_module \
    --with-stream_realip_module \
    --with-stream_geoip_module=dynamic \
    --with-stream_ssl_preread_module

gmake && gmake install

编译完成后，默认安装目录为 /usr/local/openresty。Nginx 安装在 /usr/local/openresty/nginx 目录下。

## 4、OpenResty 集成的模块

在 OpenResty 中使用 Lua 是非常方便的，既可以在配置文件中通过 OpenResty 定义的指令区域直接编写 Lua 语法命令，也可以通过引用方式调用外部 Lua 脚本文件。

OpenResty 提供了很多非常实用的 Nginx 模块和 Lua 支持库，模块说明如下表所示。

| 模块文件夹名 | 模块说明 |
| array-var-nginx-module | 一个 Nginx 模块，向 Nginx 配置文件添加对数组类型变量的支持 |
| drizzle-nginx-module | 实现与 MySQL 或 Drizzle 数据库的代理连接支持 |
| echo-nginx-module | 该模块封装了大量 Nginx 内部 API，用于流式输入和输出、并行/顺序子请求、定时器和休眠，以及各种元数据访问 |
| encrypted-session-nginx-module | 该模块为基于 Mac 系统的 AES-256，为 Nginx 变量提供加密和解密支持 |
| form-input-nginx-module | 该模块读取编码为 application/x-www-form-urlencoded 的 HTTP POST 和 PUT 请求体，并将请求体中的参数解析为 Nginx 变量 |
| headers-more-nginx-module | 该模块允许用户添加、设置或清除指定响应或请求头 |
| iconv-nginx-module | 该模块使用 libiconv 转换不同编码的字符，它既可以处理 Nginx 变量，也可以作为输出过滤器处理响应体 |
| lua-cjson | 可为 Lua 提供快速的 JSON 解析和编码支持 |
| lua-rds-parser | 该 Lua 库可以用于将由 Drizzle Nginx 模块和 Postgres Nginx 模块生成的 Resty-DBD-Stream 流格式的数据解析为 Lua 数据结构 |
| lua-redis-parser | 该 Lua 库实现了一个精简而快速的将 Redis Raw 格式解析为 Lua 数据结构的解析器，同时还构造了一个 Redis Raw 格式请求函数 |
| lua-resty-core | 使用 LuaJIT FFI 实现的 Lua Nginx 模块 Lua API |
| lua-resty-dns | 基于 Lua Nginx 模块 cosocket API 的非阻塞域名解析支持库 |
| lua-resty-limit-traffic | 用于限制和控制流量的 Lua 支持库 |
| lua-resty-lock | 该 Lua 库基于 Lua Nginx 模块的共享内存字典实现了一个简单的非阻塞互斥锁 API，主要用于消除 dog-pile effects |
| lua-resty-lrucache | Lua-land LRU 缓存支持库 |
| lua-resty-memcached | 基于 Lua Nginx 模块 cosocket API 的 Lua Memcached 客户端驱动支持库 |
| lua-resty-mysql | 基于 Lua Nginx 模块 cosocket API 的 Lua MySQL 客户端驱动支持库 |
| lua-resty-redis | 基于 Lua Nginx 模块 cosocket API 的 Lua Redis 客户端驱动支持库 |
| lua-resty-shell | 该支持库可以实现通过 Lua 调用 shell 脚本的支持 |
| lua-resty-signal | 该支持库用于向 UNIX 进程发送信号 |
| lua-resty-string | 为 Lua Nginx 模块提供字符串实用程序和公共哈希函数的 Lua 库 |
| lua-resty-upload | 基于 Lua Nginx 模块 cosocket API 的 HTTP 文件上传流式阅读器和解析器支持库 |
| lua-resty-upstream-healthcheck | Nginx 上游服务器运行状况的主动检查支持库 |
| lua-resty-websocket | 基于 Lua Nginx 模块 cosocket API 的非阻塞 WebSocket 服务器和非阻塞 WebSocket 客户端支持库 |
| lua-tablepool | 基于 LuaJIT 的 Lua 表格资源回收池支持库 |
| memc-nginx-module | 该模块扩展了标准的 Memcached 模块，几乎支持全部 Memcached ASCII 协议，它允许用户为 Memcached 服务器定义一个 Rest 接口， 通过子请求或独立的伪请求在 Nginx 服务器中以非常高效的方式访问 Memcached 服务器 |
| ngx_coolkit | 该模块是一些小的、有用的 Nginx 附加组件集合 |
| ngx_devel_kit | 该模块旨在以其为基础为其他模块提供扩展 Nginx Web 服务器的核心功能支持 |
| ngx_lua_upstream | 该模块向 Lua Nginx 模块公开了一个 Lua API，用于 Nginx 上游服务器的操作 |
| ngx_stream_lua | Nginx Stream 代理的 Lua 脚本支持模块 |
| opm | OpenResty 的包管理工具 |
| rds-csv-nginx-module | 该 Lua 库可以用于将由 Drizzle Nginx 模块和 Postgres Nginx 模块生成的 Resty-DBD-Stream 流格式的数据解析为 CSV 数据结构 |
| rds-json-nginx-module | 该 Lua 库可以用于将由 Drizzle Nginx 模块生成的 Resty-DBD-Stream 流格式的数据解析为 JSON 数据结构 |
| redis2-nginx-module | 该模块使 Nginx 以非阻塞的方式与 Redis 2.x 服务器通信，已经完整地实现 Redis 2.0 协议支持，包括 Redis pipelining 支持 |
| redis-nginx-module | 该模块使 Nginx 以非阻塞的方式与 Redis 2.x 服务器通信，且与标准的 ngx_memcached 模块有相似的接口，但是只支持 Redis 的 GET 和 SELECT 命令 |
| resty-cli | OpenResty 的命令行实用程序的集合 |
| set-misc-nginx-module | 该模块是 Nginx 的 rewrite 模块的功能增强模块，提供了更多的指令 |
| srcache-nginx-module | 该模块为 Nginx 提供了一个透明的缓存层，可以用在 Nginx 的任意位置 |
| xss-nginx-module | 该模块提供了跨站点 AJAX 支持，目前只支持跨站点的 GET 方法 |

各模块的详细功能和使用方法请参见官方网站的相关介绍。