# Docker Compose yml 配置文件及常用指令简介

> 原文：[`c.biancheng.net/view/3172.html`](http://c.biancheng.net/view/3172.html)

Docker Compose 使用 YAML 文件来定义多服务的应用。YAML 是 JSON 的一个子集，因此也可以使用 JSON。

Docker Compose 默认使用文件名 docker-compose.yml。当然，也可以使用 -f 参数指定具体文件。

如下是一个简单的 Compose 文件的示例，它定义了一个包含两个服务（web-fe 和 redis）的小型 Flask 应用。

这是一个能够对访问者进行计数并将其保存到 Redis 的简单的 Web 服务。

version: "3.5"
services:
web-fe:
build: .
command: python app.py
ports:
- target: 5000
published: 5000
networks:
- counter-net
volumes:
- type: volume
source: counter-vol
target: /code
redis:
image: "redis:alpine"
networks:
counter-net:

networks:
counter-net:

volumes:
counter-vol:

在深入研究之前粗略观察文件的基本结构，首先可以注意到，它包含 4 个一级 key：version、services、networks、volumes。

version 是必须指定的，而且总是位于文件的第一行。它定义了 Compose 文件格式（主要是 API）的版本。

> 注意，version 并非定义 Docker Compose 或 Docker 引擎的版本号。

示例中 Compose 文件将使用版本 3 及以上的版本。

services 用于定义不同的应用服务。上边的例子定义了两个服务：一个名为 web-fe 的 Web 前端服务以及一个名为 redis 的内存数据库服务。

Docker Compose 会将每个服务部署在各自的容器中。

networks 用于指引 Docker 创建新的网络。默认情况下，Docker Compose 会创建 bridge 网络。

这是一种单主机网络，只能够实现同一主机上容器的连接。当然，也可以使用 driver 属性来指定不同的网络类型。

下面的代码可以用来创建一个名为 over-net 的 Overlay 网络，允许独立的容器（standalone container）连接（attachable）到该网络上。

networks:
over-net:
driver: overlay
attachable: true

volumes 用于指引 Docker 来创建新的卷。

上面例子中的 Compose 文件使用的是 v3.5 版本的格式，定义了两个服务，一个名为 counter-net 的网络和一个名为 counter-vol 的卷。

更多的信息在 services 中，下面仔细分析一下。

Compose 文件中的 services 部分定义了两个二级 key：web-fe 和 redis。

它们各自定义了一个应用程序服务。需要明确的是，Docker Compose 会将每个服务部署为一个容器，并且会使用 key 作为容器名字的一部分。

本例中定义了两个 key：web-fe 和 redis。因此 Docker Compose 会部署两个容器，一个容器的名字中会包含 web-fe，而另一个会包含 redis。

web-fe 的服务定义中，包含如下指令。

#### 1) build

指定 Docker 基于当前目录（.）下 Dockerfile 中定义的指令来构建一个新镜像。该镜像会被用于启动该服务的容器。

#### 2) command

python app.py 指定 Docker 在容器中执行名为 app.py 的 Python 脚本作为主程序。

因此镜像中必须包含 app.py 文件以及 Python，这一点在 Dockerfile 中可以得到满足。

#### 3) ports

指定 Docker 将容器内（-target）的 5000 端口映射到主机（published）的 5000 端口。

这意味着发送到 Docker 主机 5000 端口的流量会被转发到容器的 5000 端口。容器中的应用监听端口 5000。

#### 4) networks

使得 Docker 可以将服务连接到指定的网络上。这个网络应该是已经存在的，或者是在 networks 一级 key 中定义的网络。

对于 Overlay 网络来说，它还需要定义一个 attachable 标志，这样独立的容器才可以连接上它（这时 Docker Compose 会部署独立的容器而不是 Docker 服务）。

#### 5) volumes

指定 Docker 将 counter-vol 卷（source:）挂载到容器内的 /code（target:）。

counter-vol 卷应该是已存在的，或者是在文件下方的 volumes 一级 key 中定义的。

综上，Docker Compose 会调用 Docker 来为 web-fe 服务部署一个独立的容器。该容器基于与 Compose 文件位于同一目录下的 Dockerfile 构建的镜像。

基于该镜像启动的容器会运行 app.py 作为其主程序，将 5000 端口暴露给宿主机，连接到 counter-net 网络上，并挂载一个卷到/code。

从技术上讲，本例并不需要配置 command: python app.py。因为镜像的 Dockerfile 已经将 python app.py 定义为了默认的启动程序。

但是，本例主要是为了展示其如何执行，因此也可用于覆盖 Dockerfile 中配置的 CMD 指令。

redis 服务的定义相对比较简单。

#### 6) image

redis:alpine 使得 Docker 可以基于 redis:alpine 镜像启动一个独立的名为 redis 的容器。

这个镜像会被从 Docker Hub 上拉取下来。

#### 7) networks

配置 redis 容器连接到 counter-net 网络。

由于两个服务都连接到 counter-net 网络，因此它们可以通过名称解析到对方的地址。了解这一点很重要，本例中上层应用被配置为通过名称与 Redis 服务通信。