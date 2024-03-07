# Nginx Ingress 注解 Annotations

> 原文：[`www.weixueyuan.net/a/884.html`](http://www.weixueyuan.net/a/884.html)

Nginx Ingress 注解使用在 Ingress 资源实例中，用以设置当前 Ingress 资源实例中 Nginx 虚拟主机的相关配置，对应配置的是 Nginx 当前虚拟主机的 server 指令域内容。在与 Nginx Ingress 配置映射具有相同功能配置时，将按照所在指令域层级遵循 Nginx 配置规则覆盖。

Nginx Ingress 注解按照配置功能有如下分类。

## 1、Nginx 原生配置指令

支持在注解中添加 Nginx 原生配置指令。配置说明如下表所示。

| 注解  | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/server-snippet | string | 在 server 指令域添加 Nginx 配置指令 |
| nginx.ingress.kubernetes.io/configuration-snippet | string | 在 location 指令域添加 Nginx 配置指令 |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    annotations:
        nginx.ingress.kubernetes.io/server-snippet: |
            location / {
                return 302 /coffee;
            }
spec:
    rules:
    - host: web.nginxbar.org
      http:
            paths:
            - path: /tea
              backend:
                  serviceName: tea-svc
                  servicePort: 80
            - path: /coffee
            backend:
                serviceName: coffee-svc
                servicePort: 80
```

## 2、通用配置

Nginx 虚拟主机中的通用配置。通用配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/enable-access-log | true 或 false | 对当前虚拟主机设置是否启用访问日志，默认为真 |
| nginx.ingress.kubernetes.io/server-alias | string | 为 Nginx 添加更多的主机名，同 Nginx 配置指令 server name |
| nginx.ingress.kubernetes.io/app-root | string | 将当前虚拟主机根目录的访问 302 跳转到当前指定的路径 |
| nginx.ingress.kubernetes.io/client-body-buffer-size | string | 同 Nginx 配置指令 client_body_buffer_size |
| nginx.ingress.kubernetes.io/use-regex | true 或 false | 是否对当前虚拟主机的 Nginx 指令 location 使用正则方式进行路径匹配，默认值为 false |
| nginx.ingress.kubernetes.io/custom-http-errors | []int | 根据响应码状态定义为错误状态并跳转到设置的默认后端 |
| nginx.ingress.kubernetes.io/default-backend | string | 自定义默认后端的资源对象 Service 名称，当客户端的请求没有匹配的 Nginx 规则或响应错误时，将被转发到默认后端 |
| nginx.ingress.kubernetes.io/whitelist-source-range | CIDR | 功能同 ConfigMap 配置键 whitelist-source-range |
| nginx.ingress.kubernetes.io/permanent-redirect | string | 设置永久重定向的目标地址 |
| nginx.ingress.kubernetes.io/permanent-redirect-code | number | 自定义永久重定向的响应码，默认为 301 |
| nginx.ingress.kubernetes.io/temporal-redirect | string | 设置临时重定向的目标地址 |
| nginx.ingress.kubernetes.io/from-to-www-redirect | true 或 false | 设置是否将当前虚拟主机子域名为 www 的请求跳转到当前主机域名 |
| nginx.ingress.kubernetes.io/rewrite-target | URI | 同 Nginx 配置指令 rewrite |
| nginx.ingress.kubernetes.io/enable-rewrite-log | true 或 false | 同 Nginx 配置指令 rewrite log，默认为 false |
| nginx.ingress.kubernetes.io/mirror-uri | string | 同 Nginx 配置指令 mirro |
| nginx.ingress.kubernetes.io/mirror-request-body | true 或 false | 同 Nginx 配置指令 mirror_request_body，默认为 true |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    namespace: default
    annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /tea/$1
        nginx.ingress.kubernetes.io/enable-rewrite-log: "true"
spec:
    rules:
    - host: web.nginxbar.org   # 此 service 的访问域名
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
        path: /coffee/(.+)
```

## 3、访问控制

用以设置基于流量、请求连接数、请求频率的访问控制。访问控制配置说明如下表所示。

| 注解 | 类型/选项 | 功能描述 |
| nginx.ingress.kubernetes.io/limit-rate | number | 访问流量速度限制，同 Nginx 配置指令 limit_rate |
| nginx.ingress.kubernetes.io/limit-rate-after | number | 启用访问流量速度限制的最大值，同 Nginx 配置指令 limit_rate_after |
| nginx.ingress.kubernetes.io/limit-connections | number | 节并发连接数限制，同 Nginx 配置指令 limit_conn |
| nginx.ingress.kubernetes.io/limit-rps | number | 每秒请求频率限制，burst 参数为给定值的 5 倍，响应状态码由 ConfigMap 的 limit-req-status-code 设定 |
| nginx.ingress.kubernetes.io/limit-rpm | number | 每分钟请求频率限制，burst 参数为给定值的 5 倍，响应状态码由 ConfigMap 的 limit-req-status-code 设定 |
| nginx.ingress.kubernetes.io/limit-whitelist | CIDR | 对以上限制设置基于 IP 的白名单 |

## 4、认证管理

Nginx Ingress 提供了基本认证、摘要认证和外部认证 3 种方式，为被代理服务器提供认证支持。认证管理配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/enable-global-auth | true 或 false | 如果 ConfigMap 的 global-auth-url 被设置，Nginx 会将所有的请求重定向到提供身份验证的 URL，默认为 true |
| nginx.ingress.kubernetes.io/satisfy | string | 同 Nginx 配置指令 satisfy |
| nginx.ingress.kubernetes.io/auth-type | basic 或 digest | 设置 HTTP 认证类型，支持基本和摘要两种类型 |
| nginx.ingress.kubernetes.io/auth-secret | string | 指定关联资源对象 secret 的名称 |
| nginx.ingress.kubernetes.io/auth-realm | string | 设置基本认证的提示信息 auth_basic |
| nginx.ingress.kubernetes.io/auth-url | string | 设置提供外部身份认证的 URL，由 Nginx 配置指令 auth_request 提供该功能 |
| nginx.ingress.kubernetes.io/auth-signin | string | 设置当外部认证返回 401 时跳转的 URL，通常为提示输入用户名和密码的 URL |
| nginx.ingress.kubernetes.io/auth-method | string | 指定访问外部认证 URL 的 HTTP 方法，由 Nginx 配置指令 proxy_method 提供该功能 |
| nginx.ingress.kubernetes.io/auth-request-redirect | string | 设置发送给认证服务器请求头中 X-Auth-Request-Redirect 的值 |
| nginx.ingress.kubernetes.io/auth-cache-key | string | 启用认证缓存，并设置认证缓存的关键字 |
| nginx.ingress.kubernetes.io/auth-cache-duration | string | 基于响应码设置认证缓存的有效时间 |
| nginx.ingress.kubernetes.io/auth-response-headers | string | 设置认证请求完成后传递到真实后端的头信息 |
| nginx.ingress.kubernetes.io/auth-snippet | string | 可以自定义在外部认证指令区域添加 Nginx 配置指令 |

基本认证配置如下：

```

# 创建基本认证用户名 nginxbar、密码 123456，输出文件名必须是 auth
htpasswd -bc auth nginxbar 123456

# 创建资源对象 secret 保存账号和密码
kubectl create secret generic basic-auth --from-file=auth

# 查看创建的 basic-auth
kubectl get secret basic-auth -o yaml

# 创建基本认证的 Ingress 实例
cat>auth-nginxbar-org.yaml<<EOF
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: auth-nginxbar-org
    namespace: default
    annotations:
        # 设置认证类型
        nginx.ingress.kubernetes.io/auth-type: basic
        # 关联账号和密码
        nginx.ingress.kubernetes.io/auth-secret: basic-auth
        # 显示认证提示信息
        nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required for web.nginxbar.org'
spec:
    rules:
    - host: auth.nginxbar.org   # 此 service 的访问域名
      http:
          paths:
          - backend:
              serviceName: nginx-web
              servicePort: 8080
EOF

kubectl create -f auth-nginxbar-org.yaml
```

认证转发配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: auth-nginxbar-org
    namespace: default
    annotations:
        nginx.ingress.kubernetes.io/auth-url: "http://$host/auth2"
        nginx.ingress.kubernetes.io/auth-signin: "http://$host/auth/start"
        nginx.ingress.kubernetes.io/auth-method: "POST"
        nginx.ingress.kubernetes.io/auth-cache-key: "foo",
        nginx.ingress.kubernetes.io/auth-cache-duration": "200 202 401 30m"
        nginx.ingress.kubernetes.io/auth-snippet: |
            proxy_set_header Foo-Header 42;
spec:
    rules:
    - host: auth.nginxbar.org   # 此 service 的访问域名
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
```

## 5、跨域访问

跨域访问功能配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/enable-cors | true 或 false | 是否启用跨域访问支持，默认为 false |
| nginx.ingress.kubernetes.io/cors-allow-origin | string | 允许跨域访问的域名，默认为 *，表示接受任意域名的访问 |
| nginx.ingress.kubernetes.io/cors-allow-methods | string | 允许跨域访问方法，默认为 GET、PUT、POST、DELETE、PATCH、OPTIONS |
| nginx.ingress.kubernetes.io/cors-allow-headers | string | 允许跨域访问的请求头，默认为 DNT，X-CustomHeader、Keep-Alive、User-Agent、X-Requested-With、If-Modified-Since、Cache-Control、Content-Type、Authorization |
| nginx.ingress.kubernetes.io/cors-allow-credentials | true 或 false | 设置在响应头中 Access-Control-Allow-Credentials 的值，设置是否允许客户端携带验证信息，如 cookie 等，默认为 true |
| nginx.ingress.kubernetes.io/cors-max-age | number | 设置响应头中 Access-Control-Max-Age 的值，设置返回结果可以用于缓存的最长时间，默认为 1728000 秒 |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    namespace: default
    annotations:
        nginx.ingress.kubernetes.io/cors-allow-headers: >-
            DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,
            If-Modified-Since,Cache-Control,Content-Type,Authorization
        nginx.ingress.kubernetes.io/cors-allow-methods: 'PUT, GET, POST, OPTIONS'
        nginx.ingress.kubernetes.io/cors-allow-origin: '*'
        nginx.ingress.kubernetes.io/enable-cors: "true"
        nginx.ingress.kubernetes.io/cors-max-age: 600
spec:
    rules:
    - host: web.nginxbar.org
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
          path: /
```

## 6、代理配置

Nginx 代理相关功能配置说明如下表所示。

| 注解 | 类型/选项 | 功能描述 |
| nginx.ingress.kubernetes.io/service-upstream | true 或 false | 默认 Nginx 以 Service 中 Pod 的 IP 和端口为 Upstream 中的成员列表，该参数为 true 时，将以 Service 的 ClusterIP 和端口为被代理入口，该功能避免了因 Pod 漂移带来的 Upstream 的配置变化 |
| nginx.ingress.kubernetes.io/backend-protocol | НТТР 或 HTTPS 或 GRPC 或 GRPCS 或
AJP 或 FCGI | 设置代理后端服务器的代理协议类型，默认为 HTTP |
| nginx.ingress.kubernetes.io/proxy-body-size | string | 同 Nginx 配置指令 client_max_body-size，默认为 1m |
| nginx.ingress.kubernetes.io/proxy-cookie-do-main | string | 同 Nginx 配置指令 proxy_cookie_domain |
| nginx.ingress.kubernetes.io/proxy-cookie-path | string | 同 Nginx 配置指令 proxy_cookie_path |
| nginx.ingress.kubernetes.io/proxy-connect-timeout | number | 同 Nginx 配置指令 proxy_connect_timeout |
| nginx.ingress.kubernetes.io/proxy-send-time-out | number | 同 Nginx 配置指令 proxy_send_timeout |
| nginx.ingress.kubernetes.io/proxy-read-time-out | number | 同 Nginx 配置指令 proxy_read_timeout |
| nginx.ingress.kubernetes.io/proxy-next-up-stream | string | 同 Nginx 配置指令 proxy_next_upstream |
| nginx.ingress.kubernetes.io/proxy-next-up-stream-timeout | number | 同 Nginx 配置指令 proxy_next_upstream_timeout |
| nginx.ingress.kubernetes.io/proxy-next-up-stream-tries | number | 同 Nginx 配置指令 proxy_next_upstream_tries |
| nginx.ingress.kubernetes.io/proxy-buffering | string | 同 Nginx 配置指令 proxy_buffering |
| nginx.ingress.kubernetes.io/proxy-buffers-number | number | 同 Nginx 配置指令 proxy_buffers |
| nginx.ingress.kubernetes.io/proxy-buffer-size | string | 同 Nginx 配置指令 proxy_buffer_size |
| nginx.ingress.kubernetes.io/proxy-request-buffering | string | 同 Nginx 配置指令 proxy_request_buffering |
| nginx.ingress.kubernetes.io/proxy-http-ver-sion | 1.0 或 1.1 | 同 Nginx 配置指令 proxy_http_version，默认为 1.1 |
| nginx.ingress.kubernetes.io/upstream-vhost | string | 自定义发送到上游服务器的信息头字段中 Host 的内容，相当于 Nginx 配置指令 proxy_set_header Host $host 的设置 |
| nginx.ingress.kubernetes.io/proxy-redirect-from | string | 设置要替换的源文本，同 Nginx 配置指令 proxy_redirect |
| nginx.ingress.kubernetes.io/proxy-redirect-to | string | 设置要替换的目标文本，同 Nginx 配置指 proxy redirect |
| nginx.ingress.kubernetes.io/connection-proxy-header | string | 设置发送到被代理服务器请求头中字段属性 connection 的值，相当于 Nginx 配置指令 proxy_set_header Connection 的状态为 Keep-Alive |
| nginx.ingress.kubernetes.io/x-forwarded-prefix | string | 创建并设置代理请求头属性字段 X-Forwarded-Prefix 属性，用以向后端传递请求路径 |
| nginx.ingress.kubernetes.io/http2-push-pre-load | true 或 false | 同 Nginx 配置指令 http2-push-preload，默认值为 false |

## 7、负载均衡

为方便上游服务器组的动态管理，Nginx Ingress 基于 Lua 实现了一致性哈希、基于子集的一致性哈希、轮询调度及峰值指数加权移动平均（Peak Exponentially Weighted Moving-Average，Peak EWMA）负载均衡算法。负载均衡配置说明如下表所示。

| 注解 | 类型/选项 | 功能描述 |
| nginx.ingress.kubernetes.io/upstream-hash-by | string | 同 Nginx 配置指令 hash，此处默认为一致性哈希负载算法，允许除了客户端 IP 或 cookie 之外的会话粘连 |
| nginx.ingress.kubernetes.io/upstream-hash-by-subset | true 或 false | 设置是否使用子集模式的一致性哈希负载算法，默认为 false |
| nginx.ingress.kubernetes.io/upstream-hash-by-subset-size | int | 设置子集模式中上游服务器分组的大小，默认为 3 |
| nginx.ingress.kubernetes.io/load-balance | round_robin 或 ewma | 设置负载均衡算法，基于 balancer_by_lua 模块实现，支持轮询和 Peak EWMA 两种负载算法 |

子集模式的一致性哈希负载算法是将上游服务器组中的被代理服务器分成固定数量的分组，然后把每个分组当作一致性哈希计算的虚拟节点。默认一致性哈希是按照每个被代理服务器为虚拟节点进行计算的。

Peak EWMA 负载均衡算法，是对每个 Pod 请求的往返延时（Round-Trip Time，RTT）计算移动平均值，并用该 Pod 的未完成请求数对这个平均值加权计算，计算值最小的 Pod 端点将被分配新的请求。

## 8、会话保持配置

设置基于 cookie 的会话亲缘关系，也就是会话保持功能。启用基于 cookie 的会话保持功能时，可以使同一客户端的请求始终转发给同一后端服务器。Nginx Ingress 对启用会话保持功能的 Service 集群使用一致性哈希负载算法，即使后端 Pod 数量变化，也不会对会话保持功能产生太大的影响。会话保持配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/affinity | cookie | 设置会话保持类型，目前只有 cookie 类型 |
| nginx.ingress.kubernetes.io/session-cookie-name | string | 设置 cookie 名称，默认为 INGRESSCOOKIE |
| nginx.ingress.kubernetes.io/session-cookie-path | string | 设置 cookie 字段 path 的值，默认值为当前资源实例 path 的设置。如果启用 use-regex 功能，使用正则匹配时，必须单独指定，不能使用默认值 |
| nginx.ingress.kubernetes.io/session-cookie-max-age | -- | 设置 cookie 字段 max-age 的值，表示 cookie 过期时间 |
| nginx.ingress.kubernetes.io/session-cookie-expires | -- | 为兼容旧的浏览器，设置 cookie 字段 expires 的值，表示 cookie 过期时间 |
| nginx.ingress.kubernetes.io/session-cookie-change-on-failure | true 或 false | 当会话保持的被代理服务器请求失败时，如果设置值为 true，则将下次请求更改为向另一台被代理服务器转发，否则继续向当前被代理服务器转发请求 |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    annotations:
        nginx.ingress.kubernetes.io/affinity: "cookie"
        nginx.ingress.kubernetes.io/session-cookie-name: "route"
        nginx.ingress.kubernetes.io/session-cookie-expires: "172800"
        nginx.ingress.kubernetes.io/session-cookie-max-age: "172800"

spec:
    rules:
    - host: web.nginxbar.org
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
        path: /
```

## 9、HTTPS 配置

HTTPS 功能的配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/force-ssl-redirect | true 或 false | 当客户端的 HTTPS 被外部集群进行 SSL 卸载（SSL offloading）时，仍将 HTTP 的请求强制跳转到 HTTPS 端口 |
| nginx.ingress.kubernetes.io/ssl-redirect | true 或 false | 设置当前虚拟主机支持 HTTPS 请求时，是否将 HTTP 的请求强制跳转到 HTTPS 端口，全局默认为 true |
| nginx.ingress.kubernetes.io/ssl-passthrough | true 或 false | 设置是否启用 SSL 透传 |
| nginx.ingress.kubernetes.io/auth-tls-secret | string | 设置客户端证书的资源对象名称 |
| nginx.ingress.kubernetes.io/ssl-ciphers | string | 设置 TLS 用于协商使用的加密算法组合，同 Nginx 配置指令 ssl_ciphers |
| nginx.ingress.kubernetes.io/auth-tls-verify-client | string | 是否启用客户端证书验证，同 Nginx 配置指令 ssl_verify_client |
| nginx.ingress.kubernetes.io/auth-tls-verify-depth | number | 客户端证书链的验证深度同 Nginx 配置指令 ssl_verify_depth |
| nginx.ingress.kubernetes.io/auth-tls-error-page | string | 设置客户端证书验证错误时的跳转页面 |
| nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream | true 或 false | 指定证书是否传递到上游服务器 |
| nginx.ingress.kubernetes.io/secure-verify-ca-secret | string | 设置是否启用对被代理服务器的 SSL 证书验证功能 |

HTTPS 配置样例如下：

```

# 创建 TLS 证书
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /data/apps/certs/dashboard.key -out /data/apps/certs/dashboard.crt -subj "/CN=dashboard.nginxbar.org/O=dashboard.nginxbar.org"
kubectl -n kube-system  create secret tls ingress-secret --key /data/apps/certs/dashboard.key --cert /data/apps/certs/dashboard.crt

# 创建 HTTPS 服务
cat>dashboard-ingress.yaml<<EOF
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: dashboard-ingress
    namespace: kube-system
    annotations:
        nginx.ingress.kubernetes.io/ingress.class: nginx
        # 使用 HTTPS 协议代理后端服务器
        nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
        # 启用 SSL 透传
        nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
    tls:
    - hosts:
        - dashboard.nginxbar.org
        secretName: ingress-secret
    rules:
        - host: dashboard.nginxbar.org
          http:
            paths:
            - path: /
              backend:
                serviceName: kubernetes-dashboard
                servicePort: 443
EOF

kubectl create -f dashboard-ingress.yaml

curl -k -H "Host:dashboard.nginxbar.org" https://10.103.196.209
```

Nginx-ingress 在用户没有提供证书的情况下会提供一个内置的默认 TLS 证书，如果 secretName 参数没有配置或配置错误，Nginx 会使用系统默认的证书，所以配置后仍需检查确认。

HTTPS 客户端证书身份认证配置样例如下：

```

# 创建客户端证书资源对象 default/ca-secret

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    annotations:
        # 启用客户端证书验证
        nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
        # 绑定客户端证书的资源对象名称，是命名空间 default 的 ca-secret
        nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"
        # 客户端证书链的验证深度为 1
        nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
        # 设置客户端证书验证错误时的跳转页面
        nginx.ingress.kubernetes.io/auth-tls-error-page: "http://www.mysite.com/error-cert.html"
        # 指定证书传递到上游服务器
        nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
    name: nginx-test
    namespace: default
spec:
    rules:
    - host: mydomain.com
        http:
            paths:
            - backend:
                serviceName: http-svc
                servicePort: 80
            path: /
    tls:
    - hosts:
        - mydomain.com
        secretName: tls-secret
```

## 10、“金丝雀”发布

“金丝雀”发布又称为灰度发布，灰度发布功能可以将用户请求按照指定的策略进行分割，并转发到不同的代理服务器组，通过不同的代理服务器部署应用不同版本可进行对照比较，因该方式对于新版本而言类似于使用“金丝雀”的方式进行测试，所以也叫“金丝雀”发布。
Nginx Ingress 支持 Header、cookie 和权重 3 种方式，可单独使用，也可以组合使用。“金丝雀”发布配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/canary | true 或 false | 启用“金丝雀”发布功能 |
| nginx.ingress.kubernetes.io/canary-by-header | string | 设置请求头属性字段的名称，用于根据该字段的值判断是否将请求路由到“金丝雀”服务器组，该字段值为 always 时则该请求被路由到“金丝雀”服务器组；该字段值为 never 时则不路由到“金丝雀”服务器组 |
| nginx.ingress.kubernetes.io/canary-by-header-value | string | 自定义用于判断是否路由到“金丝雀”服务器组的请求头字段值，默认为 always，必须与 canary-by-header 同时使用 |
| nginx.ingress.kubernetes.io/canary-by-cookie | string | 设置 cookie 的字段名称，用于根据该字段的值判断是否将请求路由到“金丝雀”服务器组。always 则路由到“金丝雀”服务器组；never 则永远不路由到“金丝雀”服务器组 |
| nginx.ingress.kubernetes.io/canary-weight | number | 将请求基于整数（0~100）的请求百分比随机路由到“金丝雀”服务器组；100 表示所有请求都路由到“金丝雀”服务器组；0 表示不路由任何请求到“金丝雀”服务器组 |

“金丝雀”路由规则同时存在时的优先顺序是 canary-by-header、canary-by-cookie、canary-weight。

配置样例如下：

```

# 创建主机 web.nginxbar.org 的 Ingress 资源配置
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    namespace: default
    annotations:
        nginx.ingress.kubernetes.io/ingress.class: "nginx"
spec:
    rules:
    - host: web.nginxbar.org   # 此 service 的访问域名
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080

# 创建主机 web.nginxbar.org 金丝雀组的 Ingress 资源配置
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org-canary
    namespace: default
    annotations:
        nginx.ingress.kubernetes.io/ingress.class: "nginx"
        nginx.ingress.kubernetes.io/canary: "true",
        # 根据请求头字段 CanaryByHeader 的值进行判断
        nginx.ingress.kubernetes.io/canary-by-header: "CanaryByHeader",
        # 请求头字段 CanaryByHeader 的值为 DoCanary 时，路由到“金丝雀”服务器组
        nginx.ingress.kubernetes.io/canary-by-header-value: "DoCanary",
        # 根据 Cookie 字段 CanaryByCookie 的值进行判断
        nginx.ingress.kubernetes.io/canary-by-cookie: "CanaryByCookie",
        # 随机 10%的请求路由到“金丝雀”服务器组
        nginx.ingress.kubernetes.io/canary-weight: "10",
spec:
    rules:
    - host: web.nginxbar.org   # 此 service 的访问域名
      http:
        paths:
        - backend:
            serviceName: nginx-web-canary
            servicePort: 8080
```

## 11、lua-resty-waf 模块

lua-resty-waf 是一个基于 OpenResty 的高性能 Web 应用防火墙，对当前虚拟主机的访问可以按照相关防火墙规则进行访问过滤。模块配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/lua-resty-waf | string | 设置 WAF 防火墙的工作模式，inactive 表示不执行任何操作；active 表示启用：simulate 模式下，如果给定请求有匹配的规则，它将记录一条警告消息而不进行处理。这有助于在完全部署规则之前调试规则并消除可能的误报 |
| nginx.ingress.kubernetes.io/lua-resty-waf-debug | true 或 false | 设置是否启用调试功能，默认值为 false |
| nginx.ingress.kubernetes.io/lua-resty-waf-ignore-rulesets | string | 设置忽略规则集的名称，当某些规则集（如 sqli 或 xss crs 规则集）太容易误报或不适用时，可通过该设置进行忽略处理 |
| nginx.ingress.kubernetes.io/lua-resty-waf-extra-rules | string | 设置自定义的规则 |
| nginx.ingress.kubernetes.io/lua-resty-waf-allow-unknown-content-types | true 或 false | 设置在发送了不在允许内容类型表中的内容类型头时是否继续处理请求。默认允许的为 text/html、text/json、application/json 的文档类型，默认值为 false |
| nginx.ingress.kubernetes.io/lua-resty-waf-score-threshold | number | 设置请求异常评分的阈值，如果超过这个阈值，则拒绝该请求，默认值为 5 |
| nginx.ingress.kubernetes.io/lua-resty-waf-process-multipart-body | true 或 false | 设置是否使用 lua-resty-upload 模块对 multipart/form-data 类型请求体的处理，默认为 true |

## 12、ModSecurity 模块配置

ModSecurity 是一个开源的 Web 应用防火墙。必须首先通过在 ConfigMap 中启用 Mod-Security 来加载 ModSecurity 模块。这将为所有路径启用 ModSecurity 过滤，可以手动在 Ingress 资源实例中禁用此功能。ModSecurity 模块配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/enable-mod-security | bool | 设置是否启用 ModSecurity 过滤，启用时应用推荐的规则以仅检测（Detection-Only）模式运行 |
| nginx.ingress.kubernetes.io/enable-owasp-core-rules | bool | 设置是否使用 OWASP 核心规则进行请求检测 |
| nginx.ingress.kubernetes.io/modsecurity-transaction-id | string | 设置从 Nginx 传递事务 ID，而不是在库中自动生成，有利于在 ModSecurity 中跟踪查看检测的请求，对应模块配置指令为 modsecurity_transaction_id |
| nginx.ingress.kubernetes.io/modsecurity-snippet | string | 添加模块配置指令 modsecurity_rules 的内容 |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    annotations:
        nginx.ingress.kubernetes.io/enable-modsecurity: "true"
        nginx.ingress.kubernetes.io/enable-owasp-core-rules: "true"
        nginx.ingress.kubernetes.io/modsecurity-transaction-id: "$request_id"
        nginx.ingress.kubernetes.io/modsecurity-snippet: |
        SecRuleEngine On
        SecDebugLog /tmp/modsec_debug.log
spec:
    rules:
    - host: web.nginxbar.org
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
            path: /
```

## 13、Influxdb 模块配置

通过使用 Nginx Influxdb 模块，可以用 UDP 协议将请求记录实时发送到后端的 Influxdb 服务器。Influxdb 模块配置说明如下表所示。

| 注解 | 类型 | 功能描述 |
| nginx.ingress.kubernetes.io/enable-influxdb | true 或 false | 是否启用 Influxdb 输出功能 |
| nginx.ingress.kubernetes.io/influxdb-measurement | string | 指定 Influxdb 中的 measurement 名称 |
| nginx.ingress.kubernetes.io/influxdb-port | string | 指定 Influxdb 的端口 |
| nginx.ingress.kubernetes.io/influxdb-host | string | 指定 Influxdb 的 IP 地址 |
| nginx.ingress.kubernetes.io/influxdb-server-name | string | 设置自己的应用标识 |

配置样例如下：

```

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: web-nginxbar-org
    annotations:
        nginx.ingress.kubernetes.io/enable-influxdb: "true"
        nginx.ingress.kubernetes.io/influxdb-measurement: "nginxbar-reqs"
        nginx.ingress.kubernetes.io/influxdb-port: "8089"
        nginx.ingress.kubernetes.io/influxdb-host: "192.168.2.110"
        nginx.ingress.kubernetes.io/influxdb-server-name: "nginxbar-com"
spec:
    rules:
    - host: web.nginxbar.org
      http:
        paths:
        - backend:
            serviceName: nginx-web
            servicePort: 8080
        path: /
```