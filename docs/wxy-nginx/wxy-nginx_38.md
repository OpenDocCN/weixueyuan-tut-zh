# Nginx FastCGI 模块配置简述

> 原文：[`www.weixueyuan.net/a/712.html`](http://www.weixueyuan.net/a/712.html)

Nginx 的 FastCGI 模块默认编译在 Nginx 的二进制文件中，无须单独编译。该模块配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| fastcgi_bind | address [transparent]
或 off | -- | 设置从指定的本地 IP 地址及端口号与 FastCGI 服务器建立连接，指令值可以是变量。当指令值参数为 transparent 时，允许将客户端的真实 IP 透传给 FastCGI 服务器，并以客户端真实 IP 为访问 FastCGI 服务器的源 IP，当指令值为 off 时，取消上一层指令域同名指令的配置 |
| fastegi_buffering | on 或 off | on | 设置是否启用响应数据缓冲区 |
| fastcgi_buffers | number size | 8 4k 或 8k | 设置单个连接从 FastCGI 服务器接收响应数据缓冲区的数量及单个缓冲区的大小，至少是两个 |
| fastcgi_buffer_size | size | 4k 或 8k | 设置用于读取 FastCGI 服务器响应数据第一部分的缓冲区大小，默认值根据操作系统平台的不同为 4KB 或 8KB |
| fastcgi_busy_buffers_size | size | 8k 或 16k | 限制在响应数据未完全读取完毕时忙于向客户端发送响应的缓冲区的大小，以使其余的缓冲区用于读取响应数据。该值必须大于单个缓冲区或 fastcgi_buffer_size 的大小，小于总缓冲区大小减掉一个缓冲区的大小 |
| fastcgi_limit_rate | rate | 0 | 限制从 FastCGI 服务器读取响应的每个请求的速率，单位是字节/秒，指令值为 0 表示不限制。该指令只在 fastcgi_buffer_ing 启用时有效 |
| fastcgi_max_temp_file_size | size | 1024m | 当响应数据超出响应数据缓冲区的大小时，超出部分的数据将存储于临时文件中。该指令用于设置临时文件的最大值。该指令值必须大于单个缓冲区或 fastcgi_buffer_size 的大小 |
| fastegi_temp_file_write_size | size | 8k 或 16k | 限制一次写入临时文件的数据大小。默认配置下，其大小通过 fastcgi-buffer-size 和 fastcgi-buffers 指令配置进行限制，最大值是 fastcgi_max_temp_file_size 指令的值，最小值必须大于单个缓冲区或 fastcgi_buffer_size 的大小 |
| fastcgi_temp_path | path[level1 [level2[level3]]] | fastcgi_temp | 设置临时文件存储目录 |
| fastcgi_request_buffering | on 或 off | on | 设置是否在将请求转发给 FastCGI 服务器之前先从客户端读取整个请求体。当禁用该功能时，如果已经发送请求主体，则无法将请求传递到下一个服务器 |
| fastcgi_store | on、off 或 string | off | 设置是否将 FastCGI 的响应数据在本地持久存储。当指令值为 on 时，存储路径为 root 或 alias 的设置。该指令可以为不经常变更的 FastCGI 响应文件创建本地镜像。响应数据先存储到临时文件中，再进行复制或重命名存储 |
| fastcgi_store_access | users:permissions... | user:rw | 设置创建持久存储路径的文件夹权限 |
| fastcgi_cache | zone 或 off | off | 设置一个共享内存 zone 用作缓存 |
| fastegi_cache_path | path 参数 | -- | 设置缓存文件存储路径及参数。缓存数据以 URL 的 MD5 值命名并存储在缓存目录中。 |
| fastcgi_cache_bypass | string... | -- | 设置不使用缓存响应数据的条件，指令值中至少有一个值不为空或 0 时，当前请求不使用缓存中的响应数据 |
| fastcgi_cache_key | string | -- | 设置缓存的关键字 |
| fastegi_cache_lock | on 或 off | off | 是否启用缓存锁指令。向 FastCGI 发送请求时，每次只允许一个请求按照 fastcgi_cache_key 指令设置的标识增添新的缓存数据，其他相同的请求将等待缓存中出现响应数据或该缓存锁被释放，等待时间由 fastcgi_cache_lock_timeout 指令设置 |
| fastcgi_cache_lock_age | time | 5s | 如果一个请求在该指令设定的时间内没有完成响应数据缓存的添加，则再向 FastCGI 发送一次请求 |
| fastcgi_cache_lock_timeout | time | 5s | 缓存锁超时时间。超过该时间的请求将直接从 FastCGI 读取响应 |
| fastcgi_cache_max_range_offset | number | -- | 用于设置范围请求（byte-range）请求时的最大偏移量。超出该偏移量的请求将直接从 FastCGI 读取响应 |
| fastcgi_cache_methods | GET、HEAD 或 POST ... | GET HEAD | 指定可被缓存的请求方法列表 |
| fastcgi_cache_min_uses | number | 1 | 响应数据超过设置请求次数后将被缓存 |
| fastcgi_no_cache | string... | -- | 指定字符串的值不为空或不等于 0，则不对当前请求的响应数据进行缓存 |
| fastcgi_cache_purge | string... | -- | 定义清除缓存请求条件，若指定的字符串不为空或 0，则将 fastcgi_cache_key 设置的标识的缓存清除。清除成功则返回状态码 204，商业版有效 |
| fastcgi_cache_revalidate | on 或 off | off | 设置在 HTTP 头中有字段属性 If-Modified-Since 和 If-None-Match 时是否启用重新验证 |
| fastcgi_cache_use_stale | error、timeout、 invalid_header、
updating、http_500、
http_503、http_403、
http_404、http_429、
off... | off | 当出现指定的条件时，使用已经过期的缓存响应数据 |
| fastcgi_cache_background_update | on 或 off | off | 当允许使用过期的响应数据时，设置是否启用后台子请求更新过期缓存，同时向客户端返回过期的缓存响应数据 |
| fastcgi_cache_valid | [code...]time | -- | 根据响应码设置缓存时间 |
| fastcgi_catch_stderr | string | -- | 错误响应标识。若 FastCGI 响应中包含指定的字符串，则被判断为返回了无效响应 |
| fastcgi_index | name | -- | 设置默认 index 文件 |
| fastcgi_pass | address | -- | 设置 FastCGI 服务器的 IP 地址或套接字，也可以是域名或 upstream 定义的服务器组 |
| fastcgi_pass_request_body | on 或 off | on | 设置是否将客户端请求体传递给 FastCGI 服务器 |
| fastcgi_pass_request_headers | on 或 off | on | 设置是否将客户端请求头传递给 FastCGI 服务器 |
| fastcgi_force_ranges | on 或 off | off | 无论 FastCGI 的 HTTP 响应头中是否有字段 Accept-Ranges，都启用 byte-range 请求支持 |
| fastcgi_hide_header | field | -- | 指定 FastCGI 响应数据中不向客户端传递的 HTTP 头字段名称 |
| fastcgi_pass_header | field | -- | 默认配置下，Nginx 不会将头字段属性 Status 和 X-Accel-... 传递给客户端，可通过该指令开放传递 |
| fastcgi_ignore_headers | field... | -- | 设置禁止 Nginx 处理从 FastCGI 获取响应的头字段 |
| fastcgi_connect_timeout | time | 60s | Nginx 与 FastCGI 服务器建立连接的超时时间，通常不超过 75s |
| fastcgi_keep_conn | on 或 off | off | 默认配置下，FastCGI 发送完响应数据后会立刻关闭连接，当该指令的指令值为 on 时，将启用保持连接 |
| fastcgi_ignore_client_abort | on 或 off | off | 设置当客户端关闭连接时，是否关闭与 FastCGI 服务器的连接 |
| fastcgi_read_timeout | time | 60s | 在连续两个从 FastCGI 服务器接收数据的读操作之间的间隔时间超过设定的时间时，将关闭连接 |
| fastcgi_send_timeout | time | 60s | 在连续两个发送到 FastCGI 服务器的写操作之间的间隔时间超过设定的时间时，将关闭连接 |
| fastcgi_send_lowat | size | 0 | 设置 FreeBSD 系统中使用 kqueue 驱动时，socket 接口 SO_SNDLOWAT 选项的大小。在 Linux、Solaris 及 Windows 台该指令无效 |
| fastcgi_socket_keepalive | on 或 off | off | 设置是否对 FastCGI 的连接启用 so-keepalive socket 选项 |
| fastcgi_intercept_errors | on 或 off | off | 在 FastCGI 响应数据中响应码大于或等于 300 时，设置是直接传递给客户端还是重定向给 Nginx，以便 error_page 指令进行处理 |
| fastcgi_next_upstream | error、timeout、 invalid、header、
http_500、http_503、
http_403、http_404、
http_429、non_idempotent、off... | error timeout | 当出现指定的条件时，将未返回响应的客户端请求传递给 upstream 中的下一个服务器 |
| fastcgi_next_upstream_timeout | time | 0 | 设置将符合条件的请求传递给 upstream 中下一个服务器的超时时间。当指令值为 0 时，关闭该限制 |
| fastcgi_next_upstream_tries | number | 0 | 设置将符合条件的请求传递给 upstream 中下一个服务器的尝试次数。当指令值为 0 时，关闭该限制 |
| fastcgi_split_path_info | regex | -- | 定义一个正则表达式，可以将 URI 正则匹配赋值到 $fastcgi_script_name 及 $fastcgi_path_info 两个变量中，可用于获取 index.php/arg1/111/arg2/222 格式的请求参数 |
| fastegi_param | parameter value[if_not_empty] | -- | 设置发送请求到 FastCGI 时传递的请求参数。指令值为 if_not_empty 时，表示当传递的参数值不为空时才进行传递 |

对于上表，有以下几点说明。

*   除 fastcgi_cache_path 指令外，FastCGI 模块指令均可编写在 http、server、location 指令域中。
*   fastcgi_cache_purge 指令仅商业版 Nginx 才支持。开源版可通过第三方模块或自己写脚本实现。
*   fastcgi_cache_path 指令只能编写在 http 指令域中。
*   fastcgi_cache 与 fastcgi_store 指令不能在同一指令域中同时使用。
*   non_idempotent 是指 POST、LOCK、PATCH 请求方法的处理。

fastcgi_cache_path 指令值参数如下表所示。

| 参数名 | 参数格式 | 默认值 | 参数说明 |
| levels | levels | -- | 设置缓存目录的层级及命名方式 |
| use_temp_path | on 或 off | on | 当指令值为 on 时，使用 fastcgi_temp_path 设置作为临时文件目录；当指令值为 off 时，使用缓存目录作为临时文件目录 |
| keys_zone | name:size | -- | 设置存储 cache_key 的共享内存 zone 及其大小，1MB 内存可以存储 8000 个 key |
| inactive | time | 10 分钟 | 设定时间内未被访问的缓存将被删除 |
| max_size | size | -- | 缓存数据的最大值，超出设定的最大值时，将执行一次迭代更新，并删除最近使用最少的缓存数据 |
| manager_files | number | 100 | 当执行一次迭代更新时，删除文件的最大数 |
| manager_sleep | time | 50ms | 连续两次迭代更新间的最短时间间隔 |
| manager_threshold | time | 200ms | 执行一次迭代更新时的最大执行时间 |
| loader_files | number | 100 | 每次迭代加载时，加载缓存目录中缓存数据的最大文件数 |
| loader_sleep | time | 50ms | 连续两次迭代加载间的最短时间间隔 |
| loader_threshold | time | 200ms | 每次迭代加载时的最大执行时间 |
| purger | on 或 off | off | 是否启用缓存清除功能。仅商业版有效 |
| purger_files | number | 10 | 每次迭代清除时，清除缓存目录中缓存数据的最大文件数。仅商业版有效 |
| purger_sleep | time | 50ms | 连续两次迭代清除间的最短时间间隔。仅商业版有效 |
| purger_threshold | time | 50ms | 每次迭代清除时的最大执行时间。仅商业版有效 |