# Docker 容器常用命令

> 原文：[`c.biancheng.net/view/3152.html`](http://c.biancheng.net/view/3152.html)

下面介绍一些 Docker 容器中常用的命令。

#### 1) docker container run

启动新容器的命令。该命令的最简形式接收镜像和命令作为参数。镜像用于创建容器，而命令则是希望容器运行的应用。

docker container run -it ubuntu /bin/bash 命令会在前台启动一个 Ubuntu 容器，并运行 Bash Shell。

Ctrl-PQ 会断开 Shell 和容器终端之间的链接，并在退出后保持容器在后台处于运行（UP）状态。

#### 2) docker container ls

用于列出所有在运行（UP）状态的容器。如果使用 -a 标记，还可以看到处于停止（Exited）状态的容器。

#### 3) docker container exec

用于在运行状态的容器中，启动一个新进程。该命令在将 Docker 主机 Shell 连接到一个运行中容器终端时非常有用。

docker container exec -it <container-name or container-id> bash 命令会在容器内部启动一个 Bash Shell 进程，并连接到该 Shell。

为了使该命令生效，用于创建容器的镜像必须包含 Bash Shell。

#### 4) docker container stop

此命令会停止运行中的容器，并将状态置为 Exited(0)。

该命令通过发送 SIGTERM 信号给容器内 PID 为 1 的进程达到目的。

如果进程没有在 10s 之内得到清理并停止运行，那么会接着发送 SIGKILL 信号来强制停止该容器。

docker container stop 可以接收容器 ID 以及容器名称作为参数。

#### 5) docker container start

重启处于停止（Exited）状态的容器。可以在 docker container start 命令中指定容器的名称或者 ID。

#### 6) docker container rm

删除停止运行的容器。可以通过容器名称或者 ID 来指定要删除的容器。推荐首先使用 docker container stop 命令停止容器，然后使用 docker container rm 来完成删除。

#### 7) docker container inspect

显示容器的配置细节和运行时信息。该命令接收容器名称和容器 ID 作为主要参数。