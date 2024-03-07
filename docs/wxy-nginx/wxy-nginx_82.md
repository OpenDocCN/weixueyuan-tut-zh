# Nginx 在 Kubernetes（k8s）中的应用

> 原文：[`www.weixueyuan.net/nginx/kubernetes/`](http://www.weixueyuan.net/nginx/kubernetes/)

Kubernetes 简称 k8s，是 Google 开源的分布式容器管理系统，它的核心功能是如何自动化部署、扩展和管理运行于容器中的应用软件，实现对容器的部署、网络管理、负载调度、节点集群和资源的扩缩容等自动化管理功能。

Kubernetes v1.0 版本发布于 2015 年 7 月，虽然发布时间不长，但在 IT 技术领域产生了很大影响，被誉为“云时代的 Linux”。Kubernetes 支持 Docker、Rocket 和 Hyper-v 容器引擎，其中 Docker 容器引擎是基于 Go 语言开发的，它基于宿主机中操作系统上的进程级别虚拟化技术，直接利用宿主机的系统资源，比虚拟系统级别的虚拟化技术少了虚拟系统的中间层调用，具有资源占用低、镜像体积小、加载速度快等优点。

本章将以基于 Docker 容器引擎的 Kubernetes 最新版本 v1.15 介绍 Nginx 在 Kubernetes 集群中的集成应用。