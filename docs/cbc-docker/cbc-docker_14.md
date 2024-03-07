# Docker 镜像常用命令

> 原文：[`c.biancheng.net/view/3145.html`](http://c.biancheng.net/view/3145.html)

下面介绍几种镜像中常用的操作命令。

`docker image pull`是下载镜像的命令。镜像从远程镜像仓库服务的仓库中下载。

默认情况下，镜像会从 Docker Hub 的仓库中拉取。

`docker image pull alpine:latest`命令会从 Docker Hub 的 alpine 仓库中拉取标签为 latest 的镜像。

`docker image ls`列出了本地 Docker 主机上存储的镜像。可以通过 --digests 参数来查看镜像的 SHA256 签名。

`docker image inspect`命令非常有用！该命令完美展示了镜像的细节，包括镜像层数据和元数据。

`docker image rm`用于删除镜像。

`docker image rm alpine:latest`命令的含义是删除 alpine:latest 镜像。当镜像存在关联的容器，并且容器处于运行（Up）或者停止（Exited）状态时，不允许删除该镜像。