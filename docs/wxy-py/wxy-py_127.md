# Docker 启动 Redis 过程详解

> 原文：[`www.weixueyuan.net/a/760.html`](http://www.weixueyuan.net/a/760.html)

本节不介绍传统的 Redis 安装方法，而是介绍使用 Docker 来启动 Redis 服务器的方法。

## 服务器容器下载

可以通过 docker pull 命令来得到 Redis 服务器的镜像，版本选择最新的即可。

```

$ docker pull redis                    # 下载最新的 Redis 镜像
Using default tag: latest
latest: Pulling from library/redis
743f2d6c1f65: Already exists
171658c5966d: Pull complete
fbef10bd7a65: Pull complete
0b0b11956c72: Pull complete
09dbd716637e: Pull complete
d09046fd4481: Pull complete
Digest: sha256:e549a30b3c31e6305b973e0d9113a3d38d6056
6708137af9ed7cbdce5650c5cc
Status: Downloaded newer image for redis:latest
$ docker images                        # 查看是否下载完成
REPOSITORY          TAG      IMAGE ID       CREATED      SIZE
redis            latest  d3e3588af517    8 days ago      95MB
```

下载过程比较快，因为从第 15 行的输出可以看到，该镜像只有 95MB。

> 另外需要说明的是，Redis 相关的容器镜像很多，这里介绍的是 Redis 官方版本。

## 启动 Redis 容器

首先准备配置文件 redis.conf，内容如下：

bind 0.0.0.0                        # 任意本地 IP 地址
protected-mode no                    # 不需要密码就能访问
port 6379                            # 使用的 TCP 端口
tcp-backlog 511
timeout 0                            # 不超时
tcp-keepalive 300                    # TCP 连接保持时间
supervised no
pidfile /var/run/redis_6379.pid
loglevel notice
logfile ""
databases 16
always-show-logo yes
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir ./
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
slave-lazy-flush no
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble no
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes

下面需要将配置文件 redis.conf 给 Redis 使用，所以需要使用容器参数 -v，该参数将配置文件映射到容器内部的: /etc/redis/redis.conf。下面是启动容器实例：

```

$ docker run --name python_redis -p 6379:6379 -v redis.conf:/etc/redis/
     redis.conf \
-d redis redis-server            # 启动 Redis 服务器
4de3010dbba372b1ea194e9330ee4bbf6b137ba56b57e204f42abf96e9819045
$ docker ps
CONTAINER ID              IMAGE        COMMAND
CREATED                    STATUS             
PORTS                        NAMES
bb7cf9ba429a               redis        "docker-entrypoint.s…"  
3 seconds ago             Up 2 seconds       
0.0.0.0:6379->6379/tcp    python_redis
```

由于 Docker 实例显示的信息比较长，这里将其放在三行内即第 8 行到第 10 行：

*   第 8 行显示的信息是 Docker 的标识符（这个对所有的 Docker 实例都是唯一的）、使用的镜像文件和启动时运行的命令；
*   第 9 行显示的信息是 Docker 实例开始运行的时间以及 Docker 实例的状态；
*   第 10 行显示的信息是端口映射和 Docker 实例的名字。