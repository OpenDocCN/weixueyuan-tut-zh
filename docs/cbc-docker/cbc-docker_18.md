# Docker Dockerfile 简介

> 原文：[`c.biancheng.net/view/3162.html`](http://c.biancheng.net/view/3162.html)

使用 Docker 中的`docker image build` 命令会读取 Dockerfile，并将应用程序容器化。

Dockerfile 由一行行命令语句组成，并支持以 # 开头的注释行。例如：

# Test web-app to use with Pluralsight courses and Docker Deep Dive book
# Linux x64
FROM alpine

LABEL maintainer="nigelpoulton@hotmail.com"

# Install Node and NPM
RUN apk add --update nodejs nodejs-npm

# Copy app to /src
COPY . /src

WORKDIR /src

# Install dependencies
RUN  npm install

EXPOSE 8080

ENTRYPOINT ["node", "./app.js"]

使用 -t 参数为镜像打标签，使用 -f 参数指定 Dockerfile 的路径和名称，使用 -f 参数可以指定位于任意路径下的任意名称的 Dockerfile。

构建上下文是指应用文件存放的位置，可能是本地 Docker 主机上的一个目录或一个远程的 Git 库。

Dockerfile 中的 FROM 指令用于指定要构建的镜像的基础镜像。它通常是 Dockerfile 中的第一条指令。

Dockerfile 中的 RUN 指令用于在镜像中执行命令，这会创建新的镜像层。每个 RUN 指令创建一个新的镜像层。

Dockerfile 中的 COPY 指令用于将文件作为一个新的层添加到镜像中。通常使用 COPY 指令将应用代码赋值到镜像中。

Dockerfile 中的 EXPOSE 指令用于记录应用所使用的网络端口。

Dockerfile 中的 ENTRYPOINT 指令用于指定镜像以容器方式启动后默认运行的程序。

其他的 Dockerfile 指令还有 LABEL、ENV、ONBUILD、HEALTHCHECK、CMD 等。