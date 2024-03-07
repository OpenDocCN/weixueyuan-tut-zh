# Docker Compose 是什么

> 原文：[`c.biancheng.net/view/3170.html`](http://c.biancheng.net/view/3170.html)

Docker Compose 与 Docker Stack 非常类似。它能够在 Docker 节点上，以单引擎模式（Single-Engine Mode）进行多容器应用的部署和管理。

多数的现代应用通过多个更小的服务互相协同来组成一个完整可用的应用。比如一个简单的示例应用可能由如下 4 个服务组成。

*   Web 前端。
*   订单管理。
*   品类管理。
*   后台数据库。

将以上服务组织在一起，就是一个可用的应用。

部署和管理繁多的服务是困难的。而这正是 Docker Compose 要解决的问题。

Docker Compose 并不是通过脚本和各种冗长的 docker 命令来将应用组件组织起来，而是通过一个声明式的配置文件描述整个应用，从而使用一条命令完成部署。

应用部署成功后，还可以通过一系列简单的命令实现对其完整声明周期的管理。甚至，配置文件还可以置于版本控制系统中进行存储和管理。

## Docker Compose 的背景

Docker Compose 的前身是 Fig。Fig 是一个由 Orchard 公司开发的强有力的工具，在当时是进行多容器管理的最佳方案。

Fig 是一个基于 Docker 的 Python 工具，允许用户基于一个 YAML 文件定义多容器应用，从而可以使用 fig 命令行工具进行应用的部署。

Fig 还可以对应用的全生命周期进行管理。

内部实现上，Fig 会解析 YAML 文件，并通过 Docker API 进行应用的部署和管理。

在 2014 年，Docker 公司收购了 Orchard 公司，并将 Fig 更名为 Docker Compose。

命令行工具也从 fig 更名为 docker-compose，并自此成为绑定在 Docker 引擎之上的外部工具。

虽然它从未完全集成到 Docker 引擎中，但是仍然受到广泛关注并得到普遍使用。

直至今日，Docker Compose 仍然是一个需要在 Docker 主机上进行安装的外部 Python 工具。

使用它时，首先编写定义多容器（多服务）应用的 YAML 文件，然后将其交由 docker-compose 命令处理，Docker Compose 就会基于 Docker 引擎 API 完成应用的部署。