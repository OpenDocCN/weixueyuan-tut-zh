# Docker 引擎（Engine）升级教程

> 原文：[`c.biancheng.net/view/3127.html`](http://c.biancheng.net/view/3127.html)

升级 Docker 引擎（Engine）是一项重要的任务，尤其是生产环境。

下面介绍升级 Docker 引擎的关键步骤，以及一些相关的小建议和升级示例。

升级 Docker 引擎的关键步骤如下。

需要重视升级操作的每个前置条件，包括确保容器配置了正确的重启策略；在 Swarm Mode 模式下使用服务时，需要确保正确配置了 draining node。

当完成了上述前置条件的检查之后，可以通过如下步骤完成升级操作。

1) 停止 Docker 守护程序。

2) 移除旧版本 Docker。

3) 安装新版本 Docker。

4) 配置新版本的 Docker 为开机自启动。

5) 确保容器重启成功。

上面就是全部的关键步骤。下面通过具体例子来进行介绍。

不同版本的 Linux 在升级 Docker 的时候，命令可能略有区别。这里我们以 Ubuntu 16.04 和 Windows Server 2016 作为例子进行介绍。

## 在 Ubuntu 16.04 上升级 Docker CE

假设已经完成了全部的升级前置步骤并且 Docker 处于可以升级的状态，同时还可以用 root 用户身份运行升级命令。

以 root 用户运行升级命令是不推荐的，但是可以简化示例。如果不采用 root 用户运行升级命令，那最好不过了！那么需要通过 sudo 来执行下列指令。

1) 更新 APT 包列表。

$ apt-get update

2) 卸载当前 Docker。

$ apt-get remove docker docker-engine docker-ce docker.io -y

在之前的版本中，Docker 引擎的包名可能有多个。这条命令能够确保已经安装的 Docker 包全部被删除。

3) 安装新版本 Docker。

有不同版本的 Docker 可供选择，并且有多种方式可以安装 Docker。

无论是 Docker CE 还是 Docker EE，都有不止一种安装方式。例如，Docker CE 可以通过 apt 或者 deb 包管理方式进行安装，也可以使用 Docker 官网上的脚本。

接下来的命令会使用`get.docker.com`的脚本完成最新版本 Docker CE 的安装和配置。

$ wget -qO- https://get.docker.com/ | sh

4) 将 Docker 配置为开机自启动。

$ systemctl enable docker

Synchronizing state of docker.service...
Executing /lib/systemd/systemd-sysv-install enable docker

$ systemctl is-enabled docker
enabled

此时读者可能想重启自己的节点。这样可以确保刚安装的 Docker 不会对系统开机有任何的影响。

5) 检查并确保每一个容器和服务都已经重启成功。

$ docker container ls
CONTAINER ID IMAGE COMMAND CREATED STATUS

97e599aca9f5 alpine "sleep 1d" 14 minutes ago Up 1 minute

$ docker service ls
ID NAME MODE REPLICAS IMAGE
ibyotlt1ehjy prod-equus1 replicated 1/1 alpine:latest

注意，更新 Docker 还有其他的方法。这里只是介绍了基于 Ubuntu Linux 16.04 版本的方式。

## 在 Windows Server 2016 上升级 Docker EE

接下来介绍一下如何在 Windows 上将 Docker 1.12.2 版本升级到最新版本的 Docker EE。

假设已经完成了全部的准备工作，比如为容器配置了正确的重启策略，如果运行有 Swarm 服务，则需要将待升级 Swarm 节点设置为 drain 状态。

下面用到的全部命令都应当通过 PowerShell 终端执行。

1) 检查当前 Docker 版本。

> docker version
Client:
Version: 1.12.2-cs2-ws-beta
<Snip>
Server:
Version: 1.12.2-cs2-ws-beta

2) 卸载本机上可能存在的由微软公司提供的旧版本 Docker，并从 Docker 官方获取最新版本进行安装。

> Uninstall-Module DockerMsftProvider -Force

> Install-Module DockerProvider -Force

3) 更新 Docker 包。

下面的命令会强制更新（无须卸载操作）Docker，并设置为开机自启动。

> Install-Package -Name docker -ProviderName DockerProvider -Update -Force

Name Version Source Summary
---- ------- ------ -------
Docker 17.06.2-ee-6 Docker Docker for Windows Server 2016

4) 检查并确保每一个容器和服务都已经重启成功。