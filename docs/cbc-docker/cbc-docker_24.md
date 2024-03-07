# Docker Compose 常用命令

> 原文：[`c.biancheng.net/view/3168.html`](http://c.biancheng.net/view/3168.html)

下面介绍一些在使用 Docker Compose 时常用的命令。

#### 1) docker-compose up

用于部署一个 Compose 应用。

默认情况下该命令会读取名为 docker-compose.yml 或 docker-compose.yaml 的文件。

当然用户也可以使用 -f 指定其他文件名。通常情况下，会使用 -d 参数令应用在后台启动。

#### 2) docker-compose stop

停止 Compose 应用相关的所有容器，但不会删除它们。

被停止的应用可以很容易地通过 docker-compose restart 命令重新启动。

#### 3) docker-compose rm

用于删除已停止的 Compose 应用。

它会删除容器和网络，但是不会删除卷和镜像。

#### 4) docker-compose restart

重启已停止的 Compose 应用。

如果用户在停止该应用后对其进行了变更，那么变更的内容不会反映在重启后的应用中，这时需要重新部署应用使变更生效。

#### 5) docker-compose ps

用于列出 Compose 应用中的各个容器。

输出内容包括当前状态、容器运行的命令以及网络端口。

#### 6) docker-compose down

停止并删除运行中的 Compose 应用。

它会删除容器和网络，但是不会删除卷和镜像。