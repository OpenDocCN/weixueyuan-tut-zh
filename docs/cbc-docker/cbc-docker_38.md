# Docker volume 子命令

> 原文：[`c.biancheng.net/view/3205.html`](http://c.biancheng.net/view/3205.html)

通过上一节《Docker 卷与持久化数据》的学习，相信大家都有不小的收获，这里我们对上一节中用到的一些命令做了总结，如下所示。

#### 1) docker volume create

命令用于创建新卷。默认情况下，新卷创建使用 local 驱动，但是可以通过 -d 参数来指定不同的驱动。

#### 2) docker volume ls

会列出本地 Docker 主机上的全部卷。

#### 3) docker volume inspect

用于查看卷的详细信息。可以使用该命令查看卷在 Docker 主机文件系统中的具体位置。

#### 4) docker volume prune

会删除未被容器或者服务副本使用的全部卷。

#### 5) docker volume rm

删除未被使用的指定卷。