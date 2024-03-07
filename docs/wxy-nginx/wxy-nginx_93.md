# Nginx Kong（微服务网关）安装部署

> 原文：[`www.weixueyuan.net/a/887.html`](http://www.weixueyuan.net/a/887.html)

Kong 可以灵活地部署在用户的局域网中，其同样支持多种部署方式，官方在 DockerHub 上提供了 Docker 镜像，方便用户快速实现 Kong 的 Docker 化部署。部署步骤如下。

## 1、 初始化系统环境并安装 Docker 应用

配置样例如下：

```

# 安装 yum 工具
yum install -y yum-utils
# 安装 Docker 官方 yum 源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 安装 Docker 及 docker-compose 应用
yum install -y docker-ce docker-compose
# 设置 Docker 服务开机自启动
systemctl enable docker
# 启动 Docker 服务
systemctl start docker
```

## 2、Kong 应用部署

Kong 将 Nginx 的配置存储在外部数据库，可以通过自带的数据库初始化命令自动完成数据库表结构的创建和初始数据的添加，为方便一次性创建，该脚本会启动独立的容器 kong-migrations 来完成此项操作。此处脚本创建的 Kong 规划为主管理服务器，管理接口不提供外部访问，仅提供在同一虚拟网络内的 Web 工具的访问，因此设置为固定 IP。

Docker-compose 脚本内容如下：

```

version: '2.1'
# 创建名为 kong-net 的虚拟网络
networks:
    kong-net:
        ipam:
            config:
            - subnet: 172.19.0.0/24
              gateway: 172.19.0.1
        name: kong-net
services:
# 创建用于数据库初始化的独立容器
    kong-migrations:
        hostname: kong-migrations
        container_name: kong-migrations
        image: kong:latest
        command: kong migrations bootstrap
        depends_on:
            db:
                condition: service_healthy
        env_file:
            - .env_kong
        links:
            - db:db
        networks:
            - kong-net
        restart: on-failure
# 创建 Kong 容器
    kong:
        hostname: kong-nginx
        container_name: kong-nginx
        image: kong:latest
        depends_on:
            db:
                condition: service_healthy
        env_file:
            - .env_kong
        networks:
            kong-net:
                ipv4_address: 172.19.0.201
        ports:
            - "8000:8000/tcp"       # 用于监听 HTTP 协议的消费接口，实现用户请求的接入
        #   - "8001:8001/tcp"       # 用于监听 HTTP 协议的管理接口，此处关闭外部访问
            - "8443:8443/tcp"       # 用于监听 HTTPS 协议的消费接口，实现用户请求的接入
        #   - "8444:8444/tcp"       # 用于监听 HTTPS 协议的管理接口，此处关闭外部访问
        # network_mode: host        # 在高并发应用场景下，可以将 Docker 容器以 host 模式运行，
                                        # 提高传输效率
        healthcheck:
            test: ["CMD", "kong", "health"]
            interval: 10s
            timeout: 10s
            retries: 10
        restart: on-failure
# 创建 Kong 的 postgreSQL 数据库容器
    db:
        hostname: kong-postgres
        container_name: kong-postgres
        image: postgres:9.5
        env_file:
            - .env_postgress
        healthcheck:
            test: ["CMD", "pg_isready", "-U", "kong"]
            interval: 30s
            timeout: 30s
            retries: 3
        restart: on-failure
        stdin_open: true
        tty: true
        networks:
            - kong-net
                ipv4_address: 172.19.0.202
        volumes:
            - /opt/data/apps/kong/postgresql/data:/var/lib/postgresql/data
```

环境变量文件内容如下：

```

cat>.env_kong<<EOF
KONG_ADMIN_ACCESS_LOG=/dev/stdout
KONG_ADMIN_ERROR_LOG=/dev/stderr
KONG_ADMIN_LISTEN=0.0.0.0:8001
KONG_CASSANDRA_CONTACT_POINTS=db
KONG_PROXY_ACCESS_LOG=/dev/stdout
KONG_PROXY_ERROR_LOG=/dev/stderr
KONG_DATABASE=postgres
KONG_PG_DATABASE=kong-data
KONG_PG_HOST=db
KONG_PG_PASSWORD=kong
KONG_PG_USER=kong
EOF

cat>.env_postgress<<EOF
POSTGRES_DB=kong-data
POSTGRES_PASSWORD=kong
POSTGRES_USER=kong
EOF
```

## 3、Kong 的 Web 管理工具 Konga

Konga 是基于 Node.js 开发的 Kong 开源管理工具，它不仅提供了 Kong 管理接口的全部操作对象的管理功能，同时还可以对多个 Kong 节点进行管理，包括 Kong 节点的备份、还原、健康监测等，还提供了多用户的功能，让 Kong 的日常管理操作可以更加方便灵活。

Konga 通过数据存储操作用户及 Kong 管理相关的配置，此处与 Kong 共用 PostgreSQL 数据库，可通过如下命令创建并初始化数据库实例 konga：

docker run --network kong-net --rm pantsel/konga -c prepare -a postgres -u postgresql://kong:kong@172.19.0.202:5432/konga

编写 docker-compose 脚本，脚本内容如下：

```

version: '2.1'
services:
# 创建 konga 容器
    konga:
        hostname: konga
        container_name: konga
        image: pantsel/konga
        env_file:
            - .env_konga
        external_links:
            - kong-postgres:db
        ports:
            - "1337:1337/tcp"
        networks:
            - kong-net
# 加入名为 kong-net 的虚拟网络
networks:
    kong-net:
        external: true
        name: kong-net
```

环境变量文件内容如下：

```

cat>.env_konga<<EOF
DB_ADAPTER=postgres
DB_HOST=db
DB_USER=kong
DB_PASSWORD=kong
DB_DATABASE=konga
NODE_ENV=production
EOF
```

Kong 集群只需在其他服务器部署 Kong 节点并连接到同一个 PostgreSQL 数据库即可，Kong 为避免频繁地进行数据库连接，会将数据库的内容缓存在本机内存中，管理接口修改数据库配置后，Kong 的配置会在同步周期下一次开始时生效，同步周期可以通过配置文件 kong.conf 中的配置参数 db_update_frequency 进行修改，默认时间为 5 秒。