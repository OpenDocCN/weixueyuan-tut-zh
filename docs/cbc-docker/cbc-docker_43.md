# Docker Stack 命令

> 原文：[`c.biancheng.net/view/3216.html`](http://c.biancheng.net/view/3216.html)

Stack 是 Docker 原生的部署和管理多服务应用的解决方案。Stack 默认集成在 Docker 引擎中，并且提供了简单的声明式接口对应用进行部署和全生命周期管理。

下面对实际操作中经常用到的 Docker Stack 相关的命令做了一下总结，如下所示。

#### 1) docker stsack deploy

用于根据 Stack 文件（通常是 docker-stack.yml）部署和更新 Stack 服务的命令。

#### 2) docker stack ls

会列出 Swarm 集群中的全部 Stack，包括每个 Stack 拥有多少服务。

#### 3) docker stack ps

列出某个已经部署的 Stack 相关详情。该命令支持 Stack 名称作为其主要参数，列举了服务副本在节点的分布情况，以及期望状态和当前状态。

#### 4) docker stack rm

命令用于从 Swarm 集群中移除 Stack。移除操作执行前并不会进行二次确认。