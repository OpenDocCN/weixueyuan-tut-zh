# Nginx Ingress 配置映射 ConfigMap

> 原文：[`www.weixueyuan.net/a/877.html`](http://www.weixueyuan.net/a/877.html)

通过 Helm 安装 Nginx Ingress 的默认关联配置映射实例名称为 nginx-ingress-controller，用户可以通过修改资源对象 Deployment/DaemonSet 实例 nginx-ingress-controller 中的参数 --configmap 自定义关联配置映射实例的名称。

Nginx Ingress 控制器约定 Nginx Ingress 配置映射实例中的键值只能是字符串，即便是数字或布尔值时也要以字符串的形式书写，比如 "true"、"false"、"100"，"[]string" 或 "[]int" 的 Slice 类型则表示内部数据是以 "," 分隔的字符串。根据配置涉及的功能可以有如下分类。

## 1、Nginx 原生配置指令

用以提供向 Nginx 配置中添加 Nginx 原生配置指令，功能说明如下表所示。

| 名称 | 类型 | 默认值 | 功能描述 |
| main-snippet | string | "" | 在 main 指令域添加 Nginx 配置指令 |
| http-snippet | string | "" | 在 http 指令域添加 Nginx 配置指令 |
| server-snippet | string | "" | 在 server 指令域添加 Nginx 配置指令 |
| location-snippet | string | "" | 在 location 指令域添加 Nginx 配置指令 |

配置样例如下：

```

echo '
apiVersion: v1
kind: ConfigMap
data:
    http-snippet: |
        ancient_browser "UCWEB";
        ancient_browser_value oldweb;
        server {
            listen 8080;
            if ($ancient_browser) {
                rewrite ^ /${ancient_browser}.html; # 重定向到 oldweb.html
            }
        }
metadata:
    name: nginx-ingress-controller
    namespace: nginx-ingress
' | kubectl create -f -
```

## 2、通用配置

提供 Nginx 核心配置相关配置指令的配置，功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| worker-processes | string | auto | worker_processes | 参见《Nginx 进程配置指令》一节 |
| worker-cpu-affinity | string | "" | worker_cpu_affinity | 参见《Nginx 进程配置指令》一节 |
| worker-shutdown-timeout | string | 10s | worker_shutdown_timeout | 参见《Nginx 进程配置指令》一节 |
| max-worker-connections | string | -- | worker_connections | 参见《Nginx 进程配置指令》一节 |
| max-worker-open-files | string | -- | worker_rlimit_nofile | 参见《Nginx 进程配置指令》一节 |
| enable-multi-accept | bool | true | multi_accept | 参见《Nginx 进程配置指令》一节 |
| keep-alive | int | 75 | keepalive_timeout | -- |
| keep-alive-requests | int | 100 | keepalive_requests | -- |
| variables-hash-bucket-size | int | 128 | variables_hash_bucket_size | -- |
| variables-hash-max-size | int | 2048 | variables_hash_max_size | -- |
| server-name-hash-max-size | int | 1024 | server_names_hash_max_size | -- |
| server-name-hash-bucket-size | int | CPU 缓存行的大小 | server_names_hash_bucket_size | -- |
| map-hash-bucket-size | int | 64 | map_hash_bucket_size | -- |
| bind-address | []string | "" | listen | 设置虚拟主机绑定的 IP 地址 |
| reuse-port | bool | true | listen | 设置监听端口启用 reuseport 参数，由 Linux 内核以套接字分片方式实现进程调度 |
| disable-ipv6 | bool | false | listen | -- |
| disable-ipv6-dns | bool | false | resolver | 设置是否关闭域名解析的 IPv6 地址查找 |
| enable-underscores-in-headers | bool | false | underscores_in_headers | 参见《Nginx 处理 HTTP 请求》一节 |
| ignore-invalid-headers | bool | true | ignore_invalid_headers | 参见《Nginx 处理 HTTP 请求》一节 |
| client-header-buffer-size | string | 1k | client_header_buffer_size | 参见《Nginx 处理 HTTP 请求》一节 |
| client-header-timeout | int | 60 | client_header_timeout | 参见《Nginx 处理 HTTP 请求》一节 |
| client-body-buffer-size | string | 8k | client_body_buffer_size | 参见《Nginx 处理 HTTP 请求》一节 |
| client-body-timeout | int | 69 | client_body_timeout | 参见《Nginx 处理 HTTP 请求》一节 |
| large-client-header-buffers | string | 4 8k | large_client_header_buffers | 参见《Nginx 处理 HTTP 请求》一节 |
| http-redirect-code | int | 308 | return | 设置 URL 跳转时的响应码，可选项为 301，302，307 和 308 |
| use-geoip | bool | true | -- | 启用 geoip 功能 |
| use-geoip2 | bool | false | -- | 启用 geoip2 功能，由第三方模块实现 |
| nginx-status-ipv4-whitelist | []string | 127.0.0.1 | -- | 设置允许访问路径 /nginx_status 的 IPv4 地址 |
| nginx-status-ipv6-whitelist | []string | ::1 | -- | 设置允许访问路径 /nginx_status 的 IPv6 地址 |
| server-tokens | bool | true | server_tokens | 参见《Nginx 处理 HTTP 请求》一节 |
| lua-shared-dicts | string | "" | -- | 设置 Lua 共享内存字典，Oper_Resty 扩展指令 |

配置样例如下：

```

cat>test.yaml<<EOF
apiVersion: v1
kind: ConfigMap
data:
    keep-alive:   "60"
    disable-ipv6: "true"
metadata:
    name: nginx-ingress-controller
    namespace: nginx-ingress
EOF

kubectl create -f test.yaml
```

## 3、响应数据配置

提供响应信息头修改及响应数据压缩相关功能的配置，功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| add-headers | string | "" | add_header | -- |
| use-gzip | bool | true | gzip | 启用 gzip |
| gzip-level | int | 5 | gzip_comp_level | 参见《Nginx 开启 gzip》一节 |
| gzip-types | string | application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject
application/x-font-ttf application/x-web-app-manifest+json
application/xhtml+xml application/xml font/opentype
image/svg+xml image/x-icon text/css text/java-script text/plain
text/x-component | gzip_types | 参见《Nginx 开启 gzip》一节 |
| enable-brotli | bool | false | -- | 设置是否加载 brotli 模块 |
| brotli-level | int | 4 | -- | 设置 brotli 的压缩级别 |
| brotli-types | string | application/xml+rss application/atom+xml application/javascript application/x-javascript application/json applica-tion/rss+xml
application/vnd.ms-fontobject application/x-font-ttf
application/x-web-app-manifest+json application/xhtml+xml
application/xml font/opentype image/svg+xml image/x-icon
text/css text/javascript text/plain text/x-com-ponent | -- | 设置 brotli 的压缩类型 |

## 4、访问控制

提供限制连接数、访问速度、访问连接及防火墙的配置，功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| limit-conn-zone-variable | string | $binary_remote_addr | limit_conn_zone | 参见《Nginx 并发连接数限制模块》一节 |
| limit-conn-status-code | int | 503 | limit_conn_status | 参见《Nginx 并发连接数限制模块》一节 |
| limit-rate | int | 0 | limit_rate | -- |
| limit-rate-after | int | 0 | limit_rate_after | -- |
| limit-req-status-code | int | 503 | limit_req_status | 参见《Nginx 请求频率限制模块》一节 |
| whitelist-source-range | []string []string{} | -- | allow | 设置允许访问的源 IP 地址 |
| block-cidrs | []string | "" | deny | 禁止设置 IP 地址的访问 |
| block-user-agents | []string | "" | map | 禁止设置信息头字段 User-Agent 匹配值的访问 |
| block-referers | []string | "" | map | 禁止设置信息头字段 Referer 匹配值的访问 |
| enable-modsecurity | bool | false | -- | 设置是否加载 Mod-Security 连接模块 |
| enable-owasp-modsecurity-crs | bool | false | -- | 设置是否加载 OWASP ModSecurity 核心规则库 |

## 5、HTTPS 配置

提供与 HTTPS 相关的配置，功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| ssl-ciphers | string | ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384: ECDHE-ECDSA-CHACHA20-POLY-1305:ECDHE-RSA-CHACHA20-POLY1305:
ECDHE-ECDSA-AES128-GCM-SHA256：ECDHE-RSA-AES128-GCM-SHA256:
ECDHE-ECDSA-AES256-SHA-384：ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA
-AES-128-SHA256:ECDHE-RSA-AES-128-SHA256 | ssl-ciphers | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-ecdh-curve | string | auto | ssl_ecdh_curve | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-dhparam | string | "" | ssl_dhparam | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-protocols | string | TLSv1.2 | ssl_protocols | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-early-data | bool | true | ssl_early_data | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-session-cache | bool | true | ssl_session_cache | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-session-cache-size | string | 10m | ssl_session_cache | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-session-tickets | bool | true | ssl_session_tickets | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-session-ticket-key | string | -- | ssl_session_tickets | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-session-timeout | string | 10m | ssl_session_timeout | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-buffer-size | string | 4k | ssl_buffer_size | 参见《Nginx HTTPS 服务器搭建》一节 |
| ssl-redirect | bool | true | -- | 当被配置的虚拟主机启用了 TLS 支持，则将虚拟主机的 HTTP 请求跳转到 HTTPS 请求 |
| no-tls-redirect-locations | string | /.well-known/acme-challenge | -- | 一个以“,”分隔的 location 列表，对列表中 location 的 HTTP 请求永远不会跳转到 HTTPS 请求 |

## 6、HSTS 配置

HSTS（HTTP Strict Transport Security）是一种新的 Web 安全协议，HSTS 配置启用后，将强制客户端使用 HTTPS 协议与服务器建立连接，配置映射提供的 HSTS 功能配置功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| hsts | bool | true | add_header | 是否运行 SSL 时在消息头中添加 HSTS 属性字段 |
| hsts-include-subdomains | bool | true | add_header | 是否在当前域名的所有子域中启用 HSTS |
| hsts-max-age | string | 15724800 | add_header | 设置 HSTS Header 的过期时间 |
| hsts-preload | bool | false | add_header | 设置是否启用 HSTS 的预加载支持 |

## 7、认证转发配置

提供认证转发功能的全局配置，功能说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| global-auth-url | string | "" | auth_request | 设置外部身份验证的 URL |
| global-auth-method | string | "" | proxy_method | 外部认证 URL 的 HTTP 方法 |
| global-auth-signin | string | "" | -- | 当外部认证返回 401 时跳转的 URL，通常为提示输人用户名和密码的 URL |
| global-auth-response-headers | string | "" | -- | 设置认证请求完成后传递到真实后端的头信息 |
| global-auth-request-redirect | string | "" | -- | 设置发送给认证服务器请求头中 X-Auth-Request-Redirect 的值 |
| global-auth-snippet | string | "" | -- | 可以自定义在外部认证指令区域添加 Nginx 配置指令 |
| global-auth-cache-key | string | "" | -- | 启用认证缓存，并设置认证缓存的关键字 |
| global-auth-cache-duration | string | 200 202 401 5m | -- | 基于响应码设置认证缓存的有效时间 |
| no-auth-locations | string | /.well-known/acme-challenge | -- | 一个以“,”分隔的 location 列表，列表中被记录的请求将不进行身份认证 |

## 8、代理配置

设置 Nginx 的代理功能配置，相关配置说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| retry-non-idempotent | bool | false | proxy_next_upstream | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-set-header | string | "" | proxy_set_header | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-headers-hash-max-size | int | 512 | proxy_headers_hash_max_size | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-headers-hash-bucket-size | int | 64 | proxy_headers_hash_bucket_size | 参见《Nginx HTTP 代理服务器》一节 |
| hide-headers | string array | empty | proxy_hide_header | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-body-size | string | 1m | client_max_body_size | -- |
| allow-backend-server-header | bool | false | proxy_pass_header | 设置是否允许将被代理服务器的消息头字段 Server 的值代替 Nginx 的默认值返回给客户端 |
| proxy-connect-timeout | int | 5 | proxy_connect_timeout | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-read-timeout | int | 60 | proxy_read_timeout | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-send-timeout | int | 60 | proxy_send_timeout | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-buffers-number | int | 4 | proxy_buffers | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-buffer-size | string | 4k | proxy_buffer_size | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-cookie-path | string | off | proxy_cookie_path | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-cookie-domain | string | off | proxy_cookie_domain | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-next-upstream | string | error timeout | proxy_next_upstream | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-next-upstream-timeout | int | 0 | proxy_next_upstream_timeout | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-next-upstream-tries | int | 3 | proxy_next_upstream_tries | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-redirect-from | string | off | proxy_redirect | 此处为添加要替换的源文本 |
| proxy-redirect-to | string | off | proxy_redirect | 此处为添加要替换的目标文本 |
| proxy-request-buffering | string | on | proxy_request_buffering | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-buffering | string | off | proxy_buffering | 参见《Nginx HTTP 代理服务器》一节 |
| proxy-add-original-uri-header | bool | true | proxy_set_header | 为发送到后端的请求头添加一个头属性字段 X-Original-Uri，记录原始请求 |
| use-forwarded-headers | bool | false | proxy_set_header | 设置是否使用传入的头属性字段 X-Forwarded |
| forwarded-for-header | string | X-Forwarded-For | proxy_set_header | 设置用于标识客户端源 IP 的头属性字段名称 X-For-warded |
| compute-full-forwarded-for | bool | false | proxy_set_header | 设置是否将远程地址附加到头属性字段 X-For-warded-For 中，而不是替换它 |
| custom-http-errors | []int | off | error_page | 该配置会自动启用 Nginx 配置指令 proxy_intercept_errors，默认是关闭的 |
| proxy-stream-timeout | string | 600s | proxy_timeout | 参见《Nginx TCP/UDP 代理》一节 |
| proxy-stream-responses | int | 1 | proxy_responses | 参见《Nginx TCP/UDP 代理》一节 |
| use-proxy-protocol | bool | false | listen | 启用 proxy_protocol 支持 |
| proxy-protocol-header-timeout | string | 5d | proxy_protocol_timeout | 设置接收 proxy-protocol 头的超时时间，可防止 TLS 传递处理程序无限期地等待已断开的连接 |
| proxy-real-ip-cidr | []string | 0.0.0.0/0 | set_real_ip_from | 当启用 proxy-protocol 时设置授信 IP，用于后端获得真实客户端 IP |
| use-http2 | bool | true | listen | 启用 HTTP2 监听 |
| http2-max-field-size | string | 4k | http2_max_field_size | 参见《Nginx HTTP2 模块配置》一节 |
| http2-max-header-size | string | 16k | http2_max_header_size | 参见《Nginx HTTP2 模块配置》一节 |
| http2-max-requests | int | 1000 | http2_max_requests | 参见《Nginx HTTP2 模块配置》一节 |

## 9、负载均衡配置

Nginx Ingress 为方便上游服务器组的动态管理，其基于 Lua 实现了轮询调度及峰值指数加权移动平均（Peak Exponentially Weighted Moving-Average，Peak EWMA）负载均衡算法。配置映射的配置为全局负载均衡的配置，详见本节的注解负载均衡说明。配置映射还提供了被代理服务器长连接的配置支持，配置说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| load-balance | string | round_robin | -- | 设置负载均衡算法，支持轮询 round_robin 和 Peak EWMA 两种模式，基于 OpenResty 的 balancer_by_lua 模块实现 |
| upstream-keepalive-connections | int | 32 | keepalive | 参见《Nginx 负载均衡模块》一节 |
| upstream-keepalive-timeout | int | 60 | keepalive_timeout | 参见《Nginx 负载均衡模块》一节 |
| upstream-keepalive-requests | int | 100 | keepalive_requests | 参见《Nginx 负载均衡模块》一节 |

## 10、日志配置

设置 Nginx 的日志功能配置，相关配置说明如下表所示。

| 名称 | 类型 | 默认值 | Nginx 指令 | 功能描述 |
| disable-access-log | bool | false | access-log | 设置 HTTP 指令域 access-log 的指令值为 off |
| access-log-params | string | "" | access-log | 设置访问日志的参数 |
| access-log-path | string | /var/log/nginx/access.log | access-log | 设置访问日志路径 |
| log-format-escape-json | bool | false | log_format | 设置日志格式为 JSON |
| log-format-upstream | string | %v-[$the_real_ip]-$remote_user[$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time
[$proxy_up-stream_name] $upstream_addr $upstream_response_length
$upstream_response_time $upstream_status $req_id | log_format | HTTP 日志模板 |
| skip-access-log-urls | []string | []string{} | access-log | 设置不进行访问日志记录的 URL，access-log 的 if 参数 |
| enable-access-log-for-default-backend | bool | false | access-log | 是否开启默认后端的访问日志记录 |
| log-format-stream | string | [$time_local] $protocol $status $bytes_sent $bytes_received $session_time | log_format | TCP/UDP 日志模板 |
| error-log-path | string | /var/log/nginx/error.log | error_log | 错误日志路径 |
| error-log-level | string | notice | error_log | 错误日志级别 |

## 11、分布式跟踪配置

设置分布式跟踪功能的配置，配置键及功能描述如下表所示。

| 名称 | 类型 | 默认值 | 功能描述 |
| generate-request-id | bool | true | 如果请求头中没有属性字段 X-Request-ID，则为该请求随机创建一个，用于分布式链路跟踪 |
| enable-opentracing | bool | false | 设置是否加载 opentracing 模块，启用分布式跟踪支持 |
| zipkin-collector-host | string | "" | 设置用于上传跟踪信息的 zipkin 主机地址 |
| zipkin-collector-port | int | 9411 | 设置用于上传跟踪信息的 zipkin 主机端口 |
| zipkin-service-name | string | nginx | 设置用于在 zipkin 中创建跟踪的服务名称 |
| zipkin-sample-rate | float | 1.0 | 设置 zipkin 跟踪的采样率 |
| jaeger-collector-host | string | "" | 设置用于上传跟踪信息的 jaeger 主机地址 |
| jaeger-collector-port | int | 6831 | 设置用于上传跟踪信息的 jaeger 主机端口 |
| jaeger-service-name | string | nginx | 设置用于在 jaeger 中创建跟踪的服务名称 |
| jaeger-sampler-type | string | const | 设置 jaeger 的采样器名称，可选项为 const、probabilistic、ratelimiting、remote |
| jaeger-sampler-param | string | 1 | 设置 jaeger 采样器的参数 |
| jaeger-sampler-host | string | http://127.0.0.1 | 设置 jaeger 采样器为 remote 时的主机地址 |
| jaeger-sampler-port | int | 5778 | 设置 jaeger 采样器为 remote 时的主机端口 |