# Nginx stream 模块简述

> 原文：[`www.weixueyuan.net/a/751.html`](http://www.weixueyuan.net/a/751.html)

Nginx 的 TCP/UDP 代理功能的模块分为核心模块和辅助模块、核心模块 stream 需要在编译配置时增加“--with-stream”参数进行编译。核心模块的全局配置指令如下表所示。

| 参数名称 | 指令值格式 | 默认值 | 参数说明 |
| listen | address:port[ssl][udp][proxy protocol] [backlog=number][rcvbuf=size] | -- | stream 监听协议及端口 |
| listen | [sndbuf=size][bind][ipv6only=on 或 off] [reuseport][so_keepalive=on 或 off 或
[keepidle]:[keepintvl]:[keepent]] | -- | stream 监听协议及端口 |
| preread_buffer_size | size | 16k | 设置每个会话数据预读缓冲区的大小 |
| preread_timeout | timeout | 30s | 设置每个会话数据预读取的超时时间 |
| proxy_protocol_timeout | timeout | 30s | 读取代理协议头的超时时间 |
| resolver | address... [valid=time][ipv6=on 或 off] | -- | 域名解析服务器地址 |
| resolver_timeout | time | 30s | 域名解析超时时间 |
| tcp_nodelay | on 或 off | on | 启用或关闭立即发送数据（tcp_nodelay）选项 |
| variables_hash_bucket_size | size | 64 | 设置变量哈希表中桶的大小 |
| variables_hash_max_size | size | 1024 | 设置变量哈希表的最大值 |

关于上表有以下几点需要说明。

*   指令 listen 使用的指令域范围为 server；
*   指令 variables_hash_bucket_size 和 variables_hash_max_size 使用的指令域范围为 stream；
*   stream 核心模块其余指令使用的指令域范围为 stream、server；
*   resolver 指令值可填写多个域名解析服务器地址，各个地址用空格分隔；
*   listen 指令值参数如下表所示。

| 参数名称 | 默认 | 参数说明 |
| ssl | -- | 在指定监听端口上启用 SSL 协议支持 |
| udp | -- | 在指定监听端口上启用 UDP 协议支持 |
| proxy_protocol | -- | 在指定监听端口上启用 proxy_protocol 协议支持 |
| backlog | -1/511 | 设置挂起连接队列的最大长度，在 FreeBSD、DragonFly BSD 和 macOS 操作系统上，设置默认值为 -1，其他平台为 511 |
| rcvbuf | -- | 设置套接字（socket）接收缓冲区（SO_RCVBUF 选项）的大小，Linux 操作系统下默认值为内核参数 net.core.rmem_default 的值 |
| sndbuf | -- | 设置套接字（socket）发送缓冲区（SO_SNDBUF 选项）的大小，Linux 操作系统下默认值为内核参数 net.core.wmem_default 的值 |
| bind | -- | address:port 指定 IP 及端口 |
| ipv6only | on | 只接收 IPv6 连接，或接收 IPv6 和 IPv4 连接 |
| reuseport | -- | 在默认情况下，所有的工作进程都会共享一个 socket 去监听同一 IP 和端口的组合。该参数启用后，允许每个工作进程由独立的 socket 去监听同一 IP 和端口的组合，内核会对传入的连接进行负载均衡。目前，它只适用于 Linux 3.9+、DragonFly BSD 和 FreeBSD 12+ |
| so_keepalive | off | 配置监听的端口启用 TCP keepalive 机制时的心跳检测参数。当指令值为 on 时，默认等同于 so_keepalive=30m::10，表示 30 分钟无数据传输时发送探测包，总共发送 10 次，发送时间间隔为系统内核参数 tcp_keepalive_intvl 的设定值 |

配置样例如下：

```

stream {
    resolver 114.114.114.114 valid=300s; 
    resolver_timeout 2s;

    upstream backend {
       server 192.168.0.1:333;
       server www.example.com:333;
    }

    server {
        listen 127.0.0.1:333 udp reuseport;
        proxy_timeout 20s;
        proxy_pass backend;
    }

    server {
        listen [::1]:12345;
        proxy_pass unix:/tmp/stream.socket;
    }
}
```

## stream 辅助模块

#### 1) ngx_stream_map_module

该模块的功能是在客户端每次连接时，Nginx 按照 map 指令域中源变量的当前值，把设定的对应值赋给新变量。该指令的语法格式如下：

map 源变量 新变量{}

这个指令使用的指令域只有 stream，指令值参数如下表所示。

| 参数名称 | 参数值 |
| default | 为新变量指定一个默认值。若不指定这个参数，新变量默认值为空 |
| hostnames | 当源变量为主机名时，允许使用主机名前缀或后缀对源变量值进行匹配 |
| include | 引入一个外部文件作为 map 的指令域内容 |
| volatile | map 默认创建的是可被缓存的变量，启用该参数后，创建的为不可被缓存的变量 |

map 指令域中，当源变量值存在相同匹配项时，匹配的顺序如下：

*   完全匹配的字符串；
*   有主机前缀的最长字符串；
*   有主机后缀的最长字符串；
*   在指令域中按自上而下的顺序最先匹配到的正则表达式；
*   default 参数给定的默认值。

map 哈希表大小指令如下表所示。

| 名称 | map 哈希表大小指令 |
| 指令 | map_hash_max_size |
| 作用域 | stream |
| 默认值 | 2048 |
| 指令说明 | map 指令中，存储变量的哈希表的大小 |

map 哈希桶大小指令如下表所示。

| 名称 | map 哈希桶大小指令 |
| 指令 | map_hash_bucket_size |
| 作用域 | stream |
| 默认值 | 32、64 或 128 |
| 指令说明 | map 指令中，存储变量的哈希桶的大小 |

配置样例如下：

```

stream{

    map $remote_addr $limit {
        127.0.0.1    “”;
        default      $binary_remote_addr;
    }

    limit_conn_zone $limit zone=addr:10m;
    limit_conn addr 1;
    server {
        listen 33060 reuseport;
        access_log  logs/tcp.log tcp;

        proxy_timeout 20s;
        proxy_pass 127.0.0.1:3306;
    }
}
```

#### 2) ngx_stream_geo_module

该模块的功能是从源变量获取 IP 地址，并根据设定的 IP 与对应值的列表对新变量进行赋值。该模块只有一个 geo 指令，指令格式如下：

geo [源变量]新变量{}

geo 指令的默认源变量是 $remote_addr，新变量默认值为空，这个指令使用的指令域只有 stream，指令值参数如下表所示。

| 参数名 | 参数描述 |
| delete  | 删除配置中已经存在的相同 IP 地址的设定 |
| default | 如果从源变量获取的 IP 无法匹配任意一个 IP 或 IP 范围时，使用这个参数的值作为新变量赋值 |
| include | 引入一个包含 IP 与对应值的外部文件 |
| ranges | 以地址段的形式定义 IP 地址，这个参数必须放在最上面 |

配置样例如下：

```

geo $country {
    ranges;
    default                   CN;
    127.0.0.0-127.0.0.0       US;
    10.1.0.0-10.1.255.255     RU;
    192.168.1.0-192.168.1.255 UK;
}

geo $country {
    default        ZZ;
    include        conf/geo.conf;
    delete         127.0.0.0/16;

    127.0.0.0/24   US;
    10.1.0.0/16    RU;
    192.168.1.0/24 UK;
}
```

#### 3) ngx_stream_geoip_module

该模块的功能首先是根据客户端的 IP 地址与 MaxMind 数据库中的城市地址信息做比对，然后再将对应的城市地址信息赋值给内置变量。

国家信息数据库指令如下表所示。

| 名称 | 国家信息数据库指令 |
| 指令 | geoip_country |
| 作用域 | stream |
| 默认值 | 1 |
| 指令说明 | 指定国家信息的 MaxMind 数据库文件路径 |

城市信息数据库指令如下表所示。

| 名称 | 城市信息数据库指令 |
| 指令 | geoip_city |
| 作用域 | stream |
| 默认值 | 1 |
| 指令说明 | 指定城市信息的 MaxMind 数据库文件路径 |

机构信息数据库指令如下表所示。

| 名称 | 机构信息数据库指令 |
| 指令 | geoip_org |
| 作用域 | stream |
| 默认值  | 1 |
| 指令说明 | 指定机构信息的 MaxMind 数据库文件路径 |

配置样例如下：

```

stream {
    geoip_country         /usr/share/GeoIP/GeoIP.dat;
    geoip_city            /usr/share/GeoIP/GeoLiteCity.dat;

    map $geoip_city_continent_code $nearest_server {
        default        example.com;
        EU          eu.example.com;
        NA          na.example.com;
        AS          as.example.com;
    }
    ...
}
```

#### 4) ngx_stream_split_clients_module

该模块会按照配置指令将一个 0～232 之间的数值根据设定的比例分割为多个数值范围，每个数值范围会被设定一个对应的给定值。用户每次请求时，指定的字符串会被计算出一个数值，该模块会将该数值所在范围对应的给定值赋值给配置中定义的变量。该功能常用来按照用户的来源 IP 进行访问流量分流。该指令的语法格式如下：

split_clients 字符串 新变量 {}

配置样例如下：

```

stream {
    split_clients "${remote_addr}AAA" $upstream {  # ${remote_addr}AAA 会被计算出一个数值
        0.5%     backend1;  # 数值在 0 ~ 21474835 之间，$upstream 被赋值 backend1
        80.0%    backend2;  # 数值在 21474836 ~ 3435973836 之间，$upstream 被赋值 backend2
        *        backend;   # 数值在 3435973837 ~ 4294967295，$upstream 被赋值 backend
    }
    server {
        listen 389;
        proxy_pass $upstream;
    }
}
```

这个指令使用的指令域只有 stream；客户端每次请求时，指定字符串会被使用 MurmurHash2 算法计算出一个 0～232（0～4294967295）之间的数值，该模块会将该数值所在范围对应的给定值赋值给配置中定义的变量。

#### 5) ngx_stream_ssl_preread_module

该模块可以在预读取阶段从 ClientHello 消息中提取信息，赋值给内置变量后供用户调用。

SSL 信息预读如下表所示。

| 名称 | SSL 信息预读 |
| 指令 | ssl_preread |
| 作用域 | stream、server |
| 可选项 | on 或 off |
| 默认值 | off |
| 指令说明 | 设置是否启用 SSL 信息预读功能 |

内置变量如下表所示。

| 变量名 | 变量说明 |
| $ssl_preread_protocol  | 客户端支持的最高 SSL 协议版本 |
| $ssl_preread_server_name | 通过 SNI 请求的服务器名称 |
| $ssl_preread_alpn_protocols | 客户通过 ALPN 公布的协议列表 |

配置样例如下：

```

stream {
    map $ssl_preread_protocol $upstream {
        ""        ssh.example.com:22;
        "TLSv1.2" new.example.com:443;
        default   tls.example.com:443;
    }

    server {
        listen      192.168.0.1:443;
        proxy_pass  $upstream;
        ssl_preread on;
    }
}
```

#### 6) ngx_stream_limit_conn_module

该模块对访问连接中含有指定变量且变量值相同的连接数进行计数，当计数值达到 limit_conn 指令设定的值时，Nginx 服务器将关闭此类连接。由于 Nginx 采用的是多进程的架构，因此该模块通过共享内存存储计数状态并实现了多个进程间的计数状态共享。

计数存储区指令如下表所示。

| 名称 | 计数存储区指令 |
| 指令 | limit_conn_zone |
| 作用域 | stream |
| 默认值 | -- |
| 指令说明 | 设定用以存储指定变量计数的共享内存区域 |

连接数设置指令如下表所示。

| 名称 | 连接数设置指令 |
| 指令 | limit_conn |
| 作用域 | stream、server |
| 默认值 | -- |
| 指令说明 | 设置指定变量并发连接的最大数 |

连接数日志级别指令如下表所示。

| 名称 | 连接数日志级别指令 |
| 指令 | limit_conn_log_level |
| 作用域 | stream、server |
| 默认值 | error |
| 可选项 | info、notice、warn、error |
| 指令说明 | 当指定变量的并发连接数达最大值时，输出日志的级别 |

配置样例如下：

```

stream {
    limit_conn_zone $binary_remote_addr zone=addr:10m; # 对客户端 IP 进行并发计数，计数内存区
                                                       # 命名为 addr，计数内存区的大小为 10MB
    server {
        limit_conn addr 1;                             # 限制客户端的并发连接数为 1
        ...
    }
}
```

配置说明如下所示：

*   limit_conn_zone 的格式如下：

limit_conn_zone key zone=name:size;

*   limit_conn_zone 的 key 可以是文本、变量或文本与变量的组合；
*   $binary_remote_addr 为 IPv4 时，占用 4B；为 IPv6 时，占用 16B；
*   limit_conn_zone 中，1MB 的内存空间可以存储 32000 个 32B 或 16000 个 64B 的变量计数状态；
*   变量计数状态在 32 位系统平台占用 32B 或 64B，在 64 位系统平台占用 64B。

#### 7) ngx_stream_access_module

这个模块可以允许或拒绝客户端的源 IP 地址进行连接。

允许连接指令如下表所示。

| 名称 | 允许连接指令 |
| 指令 | allow |
| 作用域 | stream、server |
| 可选项 | address 或 CIDR 或 unix: 或 all |
| 默认值 | -- |
| 指令说明 | 允许指定源 IP 的客户端连接 |

拒绝连续指令如下表所示。

| 名称 | 拒绝连接指令 |
| 指令 | deny |
| 作用域 | stream、server |
| 可选项 | address 或 CIDR 或 unix: 或 all |
| 默认值 | -- |
| 指令说明 | 拒绝指定源 IP 的客户端连接 |

配置样例如下：

```

stream {
    server {
        deny  192.168.1.1;          # 禁止 192.168.1.1
        allow 192.168.0.0/24;       # 允许 192.168.0.0/24 的 IP 访问
        allow 10.1.1.0/16;          # 允许 10.1.1.0/16 的 IP 访问
        allow 2001:0db8::/32;
        deny  all;
    }
}
```

Nginx 按照自上而下的顺序进行匹配。

#### 8) ngx_stream_return_module

该模块向客户端返回指定值并关闭连接。

返回值指令如下表所示。

| 名称 | 返回值指令 |
| 指令 | return |
| 作用域 | server |
| 指令说明 | 向客户端返回指定值并关闭连接 |

配置样例如下：

```

stream {
    server {
        listen 12345;
        return $time_iso8601; # 返回当前连接的时间
    }
}
```