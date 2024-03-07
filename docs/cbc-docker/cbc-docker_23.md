# Docker 使用 docker-compose 子命令管理应用

> 原文：[`c.biancheng.net/view/3174.html`](http://c.biancheng.net/view/3174.html)

在上一节《Docker docker-compose》中我们已经将应用成功的部署好了，下面再来介绍一下如何使用 Docker Compose 启动、停止和删除应用，以及获取应用状态。并且还会演示如何使用挂载的卷来实现对 Web 前端的更新。

既然应用已经启动，下面看一下如何使其停止。为了实现这一点，将子命令 up 替换成 down 即可。

由于是使用 & 启动的应用，因此它运行在前台。这意味着在终端上会打印详细的输出，从而可以很好地了解其执行过程。下面介绍一下每一行都代表什么意思。

第 1、2 行开始尝试关闭两个服务，即 Compose 文件中定义的 web-fe 和 redis。由第 3 行可知 stop 指令会发送 SIGTERM 信号。信号会被发送到每个容器中 PID 为 1 的进程。第 4～6 行显示 Redis 容器接收到信号后优雅地自行关闭。第 7、8 行表明已成功停止 Redis。第 9 行表明 web-fe 服务也被成功停止。由第 10 和 11 行可知已停止的服务被删除。第 12 行显示 counter-net 网络被删除，第 13 行显示 docker-compose up 进程退出。

需要特别注意的是，counter-vol 卷并没有被删除，因为卷应该是用于数据的长期持久化存储的。因此，卷的生命周期是与相应的容器完全解耦的。执行 `docker volume ls` 可见该卷依然存在于系统中。写到卷上的所有数据都会保存下来。

同样，执行 `docker-compose up` 过程中拉取或构建的镜像也会保留在系统中。因此，再次部署该应用将更加快捷。

下面继续介绍其他几个 `docker-compose` 子命令。使用如下命令再次启动应用，但是这次在后台启动它。

$ docker-compose up -d
Creating network "counterapp_counter-net" with the default driver
Creating counterapp_redis_1 ... done
Creating counterapp_web-fe_1 ... done

这时会发现这次启动要快很多——因为 counter-vol 卷已经存在，而且不需要去拉取和构建镜像。

使用 `docker-compose up` 命令来查看应用的状态。

$ docker-compose ps
Name Command State Ports
--------------------------------------------------------------------------
counterapp_redis_1 docker-entrypoint... Up 6379/tcp
counterapp_web-fe_1 python app.py Up 0.0.0.0:5000->5000/tcp

输出中会显示容器名称、其中运行的 Command、当前状态以及其监听的网络端口。

使用 `docker-compose top` 命令列出各个服务（容器）内运行的进程。

$ docker-compose top
counterapp_redis_1
PID USER TIME COMMAND
------------------------------------
843 dockrema 0:00 redis-server

counterapp_web-fe_1
PID USER TIME COMMAND
-------------------------------------------------
928 root 0:00 python app.py
1016 root 0:00 /usr/local/bin/python app.py

其中 PID 编号是在 Docker 主机上（而不是容器内）的进程 ID。

`docker-compose stop` 命令会停止应用，但并不会删除资源。然后再次运行 `docker-compose ps` 查看状态。

$ docker-compose stop
Stopping counterapp_web-fe_1 ... done
Stopping counterapp_redis_1 ... done

$ docker-compose ps
Name Command State
---------------------------------------------------------
counterapp_redis_1 docker-entrypoint.sh redis Exit 0
counterapp_web-fe_1 python app.py Exit 0

可以看到，停止 Compose 应用并不会在系统中删除对应用的定义，而仅将应用的容器停止。这一点可以使用 `docker container ls -a` 命令进行验证。

对于已停止的 Compose 应用，可以使用 `docker-compose rm` 命令来删除。这会删除应用相关的容器和网络，但是不会删除卷和镜像。

当然，也不会删除应用源码（项目目录下的 app.py、Dockerfile、requirements.txt 和 docker-compose.yml）。

执行 `docker-compose restart `命令重启应用。

$ docker-compose restart
Restarting counterapp_web-fe_1 ... done
Restarting counterapp_redis_1 ... done

查看执行结果。

$ docker-compose ps
Name Command State Ports
--------------------------------------------------------------------------
counterapp_redis_1 docker-entrypoint... Up 6379/tcp
counterapp_web-fe_1 python app.py Up 0.0.0.0:5000->5000/tcp

使用 `docker-compose down` 这一个命令就可以停止和关闭应用。

$ docker-compose down
Stopping counterapp_web-fe_1 ... done
Stopping counterapp_redis_1 ... done
Removing counterapp_web-fe_1 ... done
Removing counterapp_redis_1 ... done
Removing network counterapp_counter-net

应用被删除，仅留下了镜像、卷和源码。下面最后一次部署应用，然后查看卷的情况。

$ docker compose up -d
Creating network "counterapp_counter-net" with the default driver
Creating counterapp_redis_1 ... done
Creating counterapp_web-fe_1 ... done

如果查看 Compose 文件会发现，其中定义了一个名为 counter-vol 的新卷，并将其挂载到 web-fe 服务的 /code 路径上。

services:
web-fe:
<Snip>
volumes:
- type: volume
source: counter-vol
target: /code
<Snip>
volumes:
counter-vol:

当第一次部署该应用的时候，Docker Compose 会检查是否有同名的卷存在。如果不存在，则会创建它。也可使用 `docker volume ls` 命令手动查看。

$ docker volume ls
RIVER VOLUME NAME
local counterapp_counter-vol

值得注意的是，Docker Compose 会在部署服务之前创建网络和卷。这很合理，因为它们是供服务（容器）使用的底层基础资源。

如下可见，Docker Compose 会首先创建网络和卷（甚至先于构建和拉取镜像）。

$ docker-compose up -d

Creating network "counterapp_counter-net" with the default driver
Creating volume "counterapp_counter-vol" with default driver
Pulling redis (redis:alpine)...
<Snip>

再次研读 Dockerfile 中关于 web-fe 服务的定义，会看到它将卷 counter-app 挂载到容器的 /code 目录。

还会发现，/code 正是应用安装和执行的目录。由此可知，应用的代码是位于 Docker 卷中的，如下图所示。

![应用的代码位于 Docker 卷中](img/21dfdbeb73d848f8349c2b8c4d8c18de.png)
这意味着，我们在 Docker 主机对卷中文件的修改，会立刻反应到应用中。下面验证一下。

具体的验证过程包含这样几个步骤。首先在项目目录下编辑 app.py 文件，从而应用在浏览器中的页面会显示不同的文本。然后将更新的文件复制到位于 Docker 主机的卷中。最后刷新应用的 Web 页面来查看更新的内容。

因为，所有对位于 Docker 主机上的卷中内容的修改都会立刻反映在容器内的卷里。

请使用顺手的文本编辑器修改位于项目目录下的 app.py 文件，这里我们使用的是 vim。

$ vim ~/counter-app/app.py

修改第 22 行位于双引号之间的文字。这一行以 "What's up..." 开始，可在双引号内随意输入文字并保存。

更新源码后，将其复制到 Docker 主机上相应的卷中，也就是复制到一个或多个容器的挂载点（Mount Point）中。

使用 `docker volume inspect` 命令可以查看卷位于 Docker 主机的什么位置。

$ docker volume inspect counterapp_counter-vol | grep Mount

"Mountpoint": "/var/lib/docker/volumes/counterapp_counter-vol/_data",

复制文件后，该文件就会出现在 web-fe 容器的 /code 中，覆盖掉容器中原有的 /code/app.py 文件。

$ cp ~/counterapp/app.py \
/var/lib/docker/volumes/counterapp_counter-vol/_data/app.py

现在更新的 app.py 文件已经位于容器中了。请在浏览器中通过 Docker 主机的 IP 和端口 5000 连接到应用来查看更新的内容。

更新后的情况如下图所示。

![更新后的运行效果](img/698110e83b8faa687415b01f72cde220.png)
显然在生产环境中不会这样做，但是在开发环境中这确实很节省时间。