# Docker Compose 安装教程（包含多个平台）

> 原文：[`c.biancheng.net/view/3171.html`](http://c.biancheng.net/view/3171.html)

Docker Compose 可用于多种平台。下面将分别介绍在 Windows、Mac 以及 Linux 上的几种安装方法。

#### 1) 在 Windows 10 上安装 Docker Compose

在 Windows 10 上运行 Docker 的推荐工具是 Windows 版 Docker（Docker for Windows, DfW)。

Docker Compose 会包含在标准 DfW 安装包中。所以，安装 DfW 之后就已经有 Docker Compose 工具了。

在 PowerShell 或 CMD 终端中使用如下命令可以检查 Docker Compose 是否安装成功。

> docker-compose --version
docker-compose version 1.18.0, build 8dd22a96

关于在 Windows 10 上安装 Windows 版 Docker 的详细步骤请参考《Windows Docker 安装》一节。

#### 2) 在 Mac 上安装 Docker Compose

与 Windows 10 一样，Docker Compose 也作为 Mac 版 Docker（Docker for Mac, DfM）的一部分进行安装，所以一旦安装了 DfM，也就安装了 Docker Compose。

在终端中运行如下命令检查 Docker Compose 是否安装。

$ docker-compose --version
docker-compose version 1.18.0, build 8dd22a96

关于安装 Mac 版 Docker 的详细内容请参考《Mac Docker 安装》一节。

#### 3) 在 Windows Server 上安装 Docker Compose

Docker Compose 在 Windows Server 上是作为一个单独的二进制文件安装的。

因此，使用它的前提是确保在 Windows Server 上已经正确安装了 Docker。

在 PowerShell 终端中输入如下命令来安装 Docker Compose。

为了便于阅读，下面的命令使用反引号（`）来对换行进行转义，从而将多行命令合并。

下面的命令安装的是 1.18.0 版本的 Docker Compose，可以参考 GitHub 自行选择版本号（[`github.com/docker/compose/releases`](https://github.com/docker/compose/releases)）。

只需要将 URL 中的 1.18.0 替换为你希望安装的版本即可。

> Invoke-WebRequest ` "https://github.com/docker/compose/releases/download/1\
.18.0/docker-compose-Windows-x86_64.exe" `
-UseBasicParsing `
-OutFile $Env:ProgramFiles\docker\docker-compose.exe

Writing web request
Writing request stream... (Number of bytes written: 5260755)

使用 `docker-compose --version` 命令查看安装情况。

> docker-compose --version
docker-compose version 1.18.0, build 8dd22a96

Docker Compose 安装好了，只要 Windows Server 上安装有 Docker 引擎即可使用。

#### 4) 在 Linux 上安装 Docker Compose

在 Linux 上安装 Docker Compose 分为两步。

首先使用 curl 命令下载二进制文件，然后使用 chmod 命令将其置为可运行。

Docker Compose 在 Linux 上的使用，同样需要先安装有 Docker 引擎。

如下命令会下载 1.18.0 版本的 Docker Compose 到 /usr/bin/local。请在 GitHub 上查找想安装的版本（[`github.com/docker/compose/releases`](https://github.com/docker/compose/releases)），并替换 URL 中的 1.18.0。

下面的示例是一条写成多行的命令，如果要将其合并为一行，请删掉反斜杠（\）。

https% Total % Received Time Time Time Current

下载 docker-compose 二进制文件后，使用如下命令使其可执行。

$ chmod +x /usr/local/bin/docker-compose

检查安装情况以及版本。

$ docker-compose --version
docker-compose version 1.18.0, build 8dd22a9

现在就可以在 Linux 上使用 Docker Compose 了。

此外，也可以使用 pip 来安装 Docker Compose 的 Python 包。