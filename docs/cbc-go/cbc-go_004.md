# 哪些项目使用 Go 语言开发？

Go 语言从发布 1.0 版本以来备受众多开发者关注并得到广泛使用，Go 语言的简单、高效、并发特性吸引了众多传统语言开发者的加入，而且人数越来越多。

使用 Go 语言开发的开源项目非常多。早期的 Go 语言开源项目只是通过 Go 语言与传统项目进行 C 语言库绑定实现，例如 Qt、Sqlite 等；后期的很多项目都使用 Go 语言进行重新原生实现，这个过程相对于其他语言要简单一些，这也促成了大量使用 Go 语言原生开发项目的出现。

下面列举的是原生使用 Go 语言进行开发的部分项目。

## 1) Docker

Docker 是一种操作系统层面的虚拟化技术，可以在操作系统和应用程序之间进行隔离，也可以称之为容器。Docker 可以在一台物理服务器上快速运行一个或多个实例。例如，启动一个 CentOS 操作系统，并在其内部命令行执行指令后结束，整个过程就像自己在操作系统一样高效。

项目链接：[`github.com/docker/docker`](https://github.com/docker/docker)

## 2) go 语言

Go 语言自己的早期源码使用 C 语言和汇编语言写成。从 Go 1.5 版本后，完全使用 Go 语言自身进行编写。Go 语言的源码对了解 Go 语言的底层调度有极大的参考意义，建议希望对 Go 语言有深入了解的读者读一读。

项目链接：[`github.com/golang/go`](https://github.com/golang/go)

## 3) Kubernetes

Google 公司开发的构建于 Docker 之上的容器调度服务，用户可以通过 Kubernetes 集群进行云端容器集群管理。

项目链接：[`github.com/kubernetes/kubernetes`](https://github.com/kubernetes/kubernetes)

## 4) etcd

一款分布式、可靠的 KV 存储系统，可以快速进行云配置。

项目链接：[`github.com/coreos/etcd`](https://github.com/coreos/etcd)

## 5) beego

beego 是一个类似 Python 的 Tornado 框架，采用了 RESTFul 的设计思路，使用 Go 语言编写的一个极轻量级、高可伸缩性和高性能的 Web 应用框架。

项目链接：[`github.com/astaxie/beego`](https://github.com/astaxie/beego)

## 6) martini

一款快速构建模块化的 Web 应用的 Web 框架。

项目链接：[`github.com/go-martini/martini`](https://github.com/go-martini/martini)

## 7) codis

国产的优秀分布式 Redis 解决方案。

项目链接：[`github.com/CodisLabs/codis`](https://github.com/CodisLabs/codis)

## 8) delve

Go 语言强大的调试器，被很多集成环境和编辑器整合。

项目链接：[`github.com/derekparker/delve`](https://github.com/derekparker/delve)