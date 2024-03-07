# Nginx uWSGI 模块配置简述

> 原文：[`www.weixueyuan.net/a/724.html`](http://www.weixueyuan.net/a/724.html)

uWSGI 是 Python 实现 WSGI、uWSGI（uWSGI 独有的协议）、HTTP 等协议功能的 Web 服务器，Nginx 通过 ngx_http_uwsgi_module 模块实现与 uWSGI 服务器的数据交换并完成 Python 网站的请求处理。该模块默认编译在 Nginx 二进制文件中，无须单独编译。该模块的配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| uwsgi_bind | address [transparent] 或 off | -- | 设置从指定的本地 IP 地址及端口号与 uWSGI 服务器建立连接，指令值可以是变量。当指令值为 transparent 时，允许将客户端的真实 IP 透传给 uWSGI 服务器，并以客户端真实 IP 为访问 uWSGI 服务器的源 IP；当指令值为 off 时，则取消上一层指令域同名指令的配置 |
| uwsgi_buffering | on 或 off | on | 设置是否启用响应数据缓冲区 |
| uwsgi_buffers | number size | 8 4k 或 8k | 设置单个连接从 uWSGI 服务器接收响应数据缓冲区的数量及单个缓冲区的大小。至少是两个 |
| uwsgi_buffer_size | size | 4k 或 8k | 设置用于读取 uWSGI 服务器响应数据第一部分的缓冲区大小，默认值根据操作系统平台的不同为 4KB 或 8KB |
| uwsgi_busy_buffers_size | size | 8k 或 16k | 限制在响应数据未完全读取完毕时忙于向客户端发送响应的缓冲区的大小，以使其余的缓冲区用于读取响应数据。该值必须大于单个缓冲区或 uwsgi_buffer_size 的大小，小于总缓冲区大小减掉一个缓冲区的大小 |
| uwsgi_limit_rate | rate | 0 | 限制从 uWSGI 服务器读取响应的每个请求的速率，单位是字节/秒，指令值为 0 表示不限制。该指令只在 uwsgi_buffering 启用时有效 |
| uwsgi_max_temp_file_size | size | 1024m | 当响应数据超出响应数据缓冲区的大小时，超出部分的数据将存储于临时文件中。该指令设置临时文件的最大值。该值必须大于单个缓冲区或 uwsgi_buffer_size 的大小 |
| uwsgi_temp_file_write_size | size | 8k 或 16k | 限制一次写入临时文件的数据大小。默认配置下，大小通过 uwsgi-buffer-size 和 uwsgi-buffers 配置指令进行限制，最大值是 uwsgi_max_temp_filesize 指令的值，最小值必须大于单个缓冲区或 uwsgi_buffer_size 的大小 |
| uwsgi_temp_path | path[level1 [level2 [level3]]] | uwsgi_temp | 设置临时文件存储目录 |
| uwsgi_request_buffering | on 或 off | on | 设置是否在将请求转发给 uWSGI 服务器之前先从客户端读取整个请求体。若禁用该功能，如果已经发送请求主体，则无法将请求传递到下一个服务器。对于基于 HTTP/1.1 协议的分块传输请求，会强制读取完整请求体 |
| uwsgi_store | on 或 off 或 string | off | 设置是否将 uWSGI 服务器的响应数据在本地持久存储。当指令值为 on 时，存储路径为 root 或 alias 的设置。该指令可以为不经常变更的 uWSGI 服务器响应文件创建本地镜像。响应数据先存储到临时文件中再进行复制或重命名存储 |
| uwsgi_store_access | users:permissions... | user:rw | 设置创建持久存储路径的文件夹权限 |
| uwsgi_cache | zone 或 off | off | 设置一个共享内存 zone 用作缓存 |
| uwsgi_cache_path | path 参数 | -- | 设置缓存文件存储路径及参数。缓存数据以 URI 的 MD5 值命名并存储在缓存目录中 |
| uwsgi_cache_bypass | string... | -- | 设置不使用缓存响应数据的条件，指令值中至少一个值不为空或 0 时，则当前请求不使用缓存中的响应数据 |
| uwsgi_cache_key | string | -- | 设置缓存的关键字 |
| uwsgi_cache_lock | on 或 off | off | 是否启用缓存锁指令。向 uWSGI 服务器发送请求时，每次只允许一个请求按照 uwsgi_cache_key 指令设置的标识增添新的缓存数据，其他相同的请求将等待缓存中出现响应数据或该缓存锁被释放，等待时间通过 uwsgi_cache_lock_timeout 指令设置 |
| uwsgi_cache_lock_age | time | 5s | 如果一个请求在该指令设定的时间内没有完成响应数据缓存的添加，则向 uWSGI 服务器再发送一次请求 |
| uwsgi_cache_lock_timeout | time | 5s | 缓存锁超时时间。超过该时间的请求将直接从 uWSGI 服务器读取响应 |
| uwsgi_cache_max_range_offset | number | -- | 用于设置范围请求请求时的最大偏移量。超出该偏移量的请求将直接从 uWSGI 服务器读取响应 |
| uwsgi_cache_methods | GET、HEAD 或 POST... | GET HEAD | 指定可被缓存的请求方法列表 |
| uwsgi_cache_min_uses | number | 1 | 响应数据超过设置请求次数后将被缓存 |
| uwsgi_no_cache | string... | -- | 指定字符串的值不为空或不等于 0，则不对当前请求的响应数据进行缓存 |
| uwsgi_cache_purge | string | -- | 定义清除缓存请求条件，若指定的字符串不为空或 0，则对 uwsgi_cache_key 设置的标识的缓存进行清除。清除成功则返回状态码 204。仅商业版有效 |
| uwsgi_cache_revalidate | on 或 off | off | 设置在 HTTP 头中有字段属性 If-Modified-Since 和 If-None-Match 时是否启用重新验证 |
| uwsgi_cache_ use_stale | error、timeout、  invalid_header、
updating、http_500、
http_503、http_403、
http_404、http_429、
off ... | off | 当出现指定的条件时，使用已经过期的缓存响应数据 |
| uwsgi_cache_background_update | on 或 off | off | 允许使用过期的响应数据时，设置是否启用后台子请求更新过期缓存，同时向客户端返回过期的缓存响应数据 |
| uwsgi_cache_valid | [code …] time | -- | 根据响应码设置缓存时间 |
| uwsgi_pass | address | -- | 设置 uWSGI 服务器的协议、IP 地址或套接字，也可以是域名或 upstream 定义的服务器组。支持的协议有 HTTP、uWSGI、suwsgi（基于 SSL 的 uWSGI） |
| uwsgi_pass_request_body | on 或 off | on | 设置是否将客户端请求体传递给 uWSGI 服务器 |
| uwsgi_pass_request_headers | on 或 off | on | 设置是否将客户端请求头传递给 uWSGI 服务器 |
| uwsgi_force_ranges | on 或 off | off | 无论 uWSGI 服务器的 HTTP 响应头中是否有字段 Accept-Ranges，都启用 byte-range 请求支持 |
| uwsgi_hide_header | field | -- | 指定 uWSGI 服务器响应数据中不向客户端传递的 HTTP 头字段名称 |
| uwsgi_pass_header | field | -- | 默认配置下，Nginx 不会将头字段属性 Status 和 X-Accel-... 传递给客户端，可通过该指令开放传递 |
| uwsgi_ignore_headers | field … | -- | 设置禁止 Nginx 处理从 uWSGI 服务器获取响应的头字段 |
| uwsgi_modifier1 | number | 0 | 设置 uWSGI 数据包头中 modifier1 字段的值 |
| uwsgi_modifier2 | number | 0 | 设置 uWSGI 数据包头中 modified2 字段的值 |
| uwsgi_connect_timeout | time | 60s | Nginx 与 uWSGI 服务器建立连接的超时时间，通常不超过 75s |
| uwsgi_ignore_client_abort | on 或 off | off | 当客户端关闭连接时，是否关闭与 uWSGI 服务器的连接 |
| uwsgi_read_timeout | time | 60s | 在连续两个从 uWSGI 服务器接收数据的读操作之间的间隔时间超过设定的时间时，将关闭连接 |
| uwsgi_send_timeout | time | 60s | 在连续两个发送到 uWSGI 服务器的写操作之间的间隔时间超过设定的时间时，将关闭连接 |
| uwsgi_socket_keepalive | on 或 off | off | 设置是否对 uWSGI 服务器的连接启用 so-kccpalive socket 选项 |
| uwsgi_intercept_errors | on 或 off | off | 在 uWSGI 服务器响应数据中响应码大于或等于 300 时，设置是直接传递给客户端还是重定向给 Nginx，以便 error_page 指令进行处理 |
| uwsgi_next_ upstream | error、timeout、 invalid_header、
http_500、http_503、
http_403、http_404、
http_429、
non_idempotent、
off ... | error timeout | 当出现指定条件时，将未返回响应的客户端请求传递给 upstream 中的下一个服务器 |
| uwsgi_next_upstream_timeout | time | 0 | 设置将符合条件的请求传递给 upstream 中的下一个服务器的超时时间。指令值为 0 时关闭该限制 |
| uwsgi_next_upstream_tries | number | 0 | 设置将符合条件的请求传递给 upstream 中的下一个服务器的尝试次数。指令值为 0 时关闭该限制 |
| uwsgi_param | parameter value [if_not_empty] | -- | 设置发送请求到 uWSGI 服务器时传递的请求参数。指令值为 if_not_empty 时，表示传递的参数值不为空时才进行传递 |
| uwsgi_ssl_certificate | file | -- | 指定用于安全 uWSGI 服务器 SSL 身份认证的 PEM 格式服务器证书文件 |
| uwsgi_ssl_certificate_key | file | -- | 指定用于安全 uWSGI 服务器 SSL 身份认证的 PEM 格式服务器证书密钥文件 |
| uwsgi_ssl_password_file | file | -- | 指定一个包含服务器证书密钥密码的文件。文件中每行一个密码，Nginx 将遍历整个文件并尝试有效的密码 |
| uwsgi_ssl_ciphers | ciphers | DEFAULT | 指定用于安全 uWSGI 服务器 SSL 请求的 OpenSSL 格式密码套件 |
| uwsgi_ssl_crl | file | -- | 指定用于验证安全 uWSGI 服务器 SSL 证书的 PEM 格式吊销证书文件 |
| uwsgi_ssl_trusted_certificate | file | -- | 指定用于验证安全 uWSGI 服务器 SSL 证书的 PEM 格式 CA 证书文件 |
| uwsgi_ssl_name | name | uwsgi_pass 指 令指定的主机 | 允许指定并覆盖安全 uWSGI 服务器 SSL 证书验证的 主机名，将通过 SNI 向建立连接的 SSL 安全 uWSGI 服 务器进行传递 |
| uwsgi_ssl_protocols | [SSLv2] [SSLv3] [TLSv1] [TLSv1.1]
[TLSv1.2]
[TLSv1.3] | TLSv1 TLSv1.1
TLSv1.2 | 指定安全 uWSGI 服务器 SSL 协议的版本 |
| uwsgi_ssl_server_name | on 或 off | off | 启用通过 SNI 或 RFC 6066 向建立连接的 SSL 安全 uWSGI 服务器传递主机名 |
| uwsgi_ssl_session_reuse | on 或 off | off | 启用 SSL 会话重用功能 |
| uwsgi_ssl_verify | on 或 off | off | 启用安全 uWSGI 服务器的证书验证功能 |
| uwsgi_ssl_verify_depth | number | 1 | 设置安全 uWSGI 服务器的证书链的验证深度 |

关于上表，有以下几点需要说明：

*   除 uwsgi_cache_path 指令外，uWSGI 模块指令均可编写在 http、server、location 指令域中；
*   uwsgi_cache_path 指令只能编写在 http 指令域中；
*   uwsgi_cache 与 uwsgi_store 指令不可在同一指令域中同时使用；
*   non_idempotent 是指 POST、LOCK、PATCH 请求方法的处理。

uwsgi_cache_path 指令值参数如下表所示。

| 参数名 | 参数格式 | 默认值 | 参数说明 |
| levels | levels | -- | 设置缓存目录的层级及命名方式 |
| use_temp_path | on 或 off | on | 当指令值为 on 时，使用 uwsgi_temp_path 设置作为临时文件目录；当指令值为 off 时，使用缓存目录作为临时文件目录 |
| keys_zone | name:size | -- | 设置存储 cache_key 的共享内存 zone 及其大小，1MB 内存可以存储 8000 个 key |
| inactive | time | 10m | 设定时间内未被访问的缓存将被删除 |
| max_size | size | -- | 缓存数据的最大值，超出设定的最大值时将执行一次迭代更新，并删除最近使用最少的缓存数据 |
| manager_files | number | 100 | 执行一次迭代更新时删除文件的最大数 |
| manager_sleep | time | 50ms | 连续两次迭代更新间的最短时间间隔 |
| manager_threshold | time | 200ms | 执行一次迭代更新时的最大执行时间 |
| loader_files | number | 100 | 每次迭代加载时，加载缓存目录中缓存数据的最大文件数 |
| loader_sleep | time | 50ms | 连续两次迭代加载间的最短时间间隔 |
| loader_threshold | time | 200ms | 每次迭代加载时的最大执行时间 |
| purger | on 或 off | off | 是否启用缓存清除功能。仅商业版有效 |
| purger_files | number | 10 | 每次迭代清除时，清除缓存目录中缓存数据的最大文件数。仅商业版有效 |
| purger_sleep | time | 50ms | 连续两次迭代清除间的最短时间间隔。仅商业版有效 |
| purger_threshold | time | 50ms | 每次迭代清除时的最大执行时间。仅商业版有效 |