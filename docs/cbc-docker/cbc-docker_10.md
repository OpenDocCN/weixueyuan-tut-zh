# 从运维人员的角度看 Docker

> 原文：[`c.biancheng.net/view/3133.html`](http://c.biancheng.net/view/3133.html)

从运维的角度来说，我们需要掌握 Docker 的镜像下载、运行新的容器、登录新容器、在容器内运行命令，以及销毁容器。

当我们安装 Docker 的时候，会涉及两个主要组件：Docker 客户端和 Docker daemon（有时也被称为“服务端”或者“引擎”）。

daemon 实现了 Docker 引擎的 API。

使用 Linux 默认安装时，客户端与 daemon 之间的通信是通过本地 IPC/UNIX Socket 完成的（/var/run/docker.sock）；在 Windows 上是通过名为 npipe:////./pipe/docker_engine 的管道（pipe）完成的。

可以使用`docker version`命令来检测客户端和服务端是否都已经成功运行，并且可以互相通信。

> docker version
Client:
Version: 18.01.0-ce
API version: 1.35
Go version: go1.9.2
Git commit: 03596f5
Built: Wed Jan 10 20:11:05 2018
OS/Arch: linux/amd64
Experimental: false
Orchestrator: swarm

Server:
Engine:
Version: 18.01.0-ce
API version: 1.35 (minimum version 1.12)
Go version: go1.9.2
Git commit: 03596f5
Built: Wed Jan 10 20:09:37 2018
OS/Arch: linux/amd64
Experimental: false

如果能成功获取来自客户端和服务端的响应，那么可以继续后面的操作。

如果在使用 Linux 的时候，服务端返回了异常响应，则可尝试在命令的前面加上 sudo 如：`sudo docker version`。

如果加上 sudo 之后命令正常运行，那么我们需要将当前用户加入到 docker 用户组，或者给所有的命令都加上 sudo 前缀。

## 镜像

将 Docker 镜像理解为一个包含了 OS 文件系统和应用的对象会很有帮助。

如果实际操作过，就会认为与虚拟机模板类似。虚拟机模板本质上是处于关机状态的虚拟机。

在 Docker 世界中，镜像实际上等价于未运行的容器。如果作为一名开发者，则可以将镜像比作类（Class）。

在 Docker 主机上运行`docker image ls`命令。

$ docker image ls
REPOSITORY TAG IMAGE ID CREATED SIZE

如果运行命令环境是刚完成 Docker 安装的主机，或者是 Play With Docker，那么 Docker 主机中应当没有任何镜像，命令输出内容会如上所示。

在 Docker 主机上获取镜像的操作被称为拉取（pulling）。如果使用 Linux，那么会拉取 ubuntu:latest 镜像；如果使用 Windows，则会拉取 microsoft/powershell:nanoserver 镜像。

再次运行`docker image ls`命令来查看刚刚拉取的镜像。

$ docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu latest 00fd29ccc6f1 3 weeks ago 111MB

镜像包含了基础操作系统，以及应用程序运行所需的代码和依赖包。刚才拉取的 ubuntu 镜像有一个精简版的 Ubuntu Linux 文件系统，其中包含部分 Ubuntu 常用工具。

而 Windows 示例中拉取的 microsoft/powershell 镜像，则包含了带有 PowerShell 的 Windows Nano Server 操作系统。

如果拉取了如 nginx 或者 microsoft/iis 这样的应用容器，就会得到一个包含操作系统的镜像，并且在镜像中还包括了运行 Nginx 或 IIS 所需的代码。

重要的是，Docker 的每个镜像都有自己的唯一 ID。用户可以通过引用镜像的 ID 或名称来使用镜像。

如果选择使用镜像 ID，通常只需要输入 ID 开头的几个字符即可——因为 ID 是唯一的，Docker 知道用户想引用的具体镜像是哪个。

## 容器

如果已经拥有一个拉取到本地的镜像，可以使用`docker container run`命令从镜像来启动容器。

在 Linux 中启动容器的命令如下。

$ docker container run -it ubuntu:latest /bin/bash
root@6dc20d508db0:/#

在 Windows 中启动容器的命令如下。

> docker container run -it microsoft/powershell:nanoserver pwsh.exe

Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.
PS C:\>

仔细观察上面命令的输出内容，会注意到每个实例中的提示符都发生了变化。这是因为 -it 参数会将 Shell 切换到容器终端——现在已经位于容器内部了！

接下来分析一下 `docker container run` 命令。`docker container run` 告诉 Docker daemon 启动新的容器。

其中 -it 参数告诉 Docker 开启容器的交互模式并将读者当前的 Shell 连接到容器终端。接下来，命令告诉 Docker，用户想基于 ubuntu:latest 镜像启动容器（如果用户使用 Windows，则是基于 microsoft/powershell:nanoserver 镜像）。

最后，命令告诉 Docker，用户想要在容器内部运行哪个进程。对于 Linux 示例来说是运行 Bash Shell，对于 Windows 示例来说则是运行 PowerShell。

在容器内部运行`ps`命令查看当前正在运行的全部进程。

Linux 示例如下。

root@6dc20d508db0:/# ps -elf
F S UID PID PPID NI ADDR SZ WCHAN STIME TTY TIME CMD
4 S root 1 0 0 - 4560 wait 13:38 ? 00:00:00 /bin/bash
0 R root 9 1 0 - 8606 - 13:38 ? 00:00:00 ps -elf

Windows 示例如下。

PS C:\> ps

Handles NPM(K) PM(K) WS(K) CPU(s) Id SI ProcessName
------- ------ ----- ----- ------ -- -- -----------
0 5 964 1292 0.00 4716 4 CExecSvc
0 5 592 956 0.00 4524 4 csrss
0 0 0 4 0 0 Idle
0 18 3984 8624 0.13 700 4 lsass
0 52 26624 19400 1.64 2100 4 powershell
0 38 28324 49616 1.69 4464 4 powershell
0 8 1488 3032 0.06 2488 4 services
0 2 288 504 0.00 4508 0 smss
0 8 1600 3004 0.03 908 4 svchost
0 12 1492 3504 0.06 4572 4 svchost
0 15 20284 23428 5.64 4628 4 svchost
0 15 3704 7536 0.09 4688 4 svchost
0 28 5708 6588 0.45 4712 4 svchost
0 10 2028 4736 0.03 4840 4 svchost
0 11 5364 4824 0.08 4928 4 svchost
0 0 128 136 37.02 4 0 System
0 7 920 1832 0.02 3752 4 wininit
0 8 5472 11124 0.77 5568 4 WmiPrvSE

Linux 容器中仅包含两个进程。

*   PID 1：代表 /bin/bash 进程，该进程是通过 docker container run 命令来通知容器运行的。
*   PID 9：代表 ps -elf 进程，查看当前运行中进程所使用的命令/程序。

命令输出中展示的`ps -elf`进程存在一定的误导，因为这个程序在`ps`命令退出后就结束了。这意味着容器内长期运行的进程其实只有 /bin/bash。

Windows 容器运行中的进程会更多，这是由 Windows 操作系统工作方式决定的。

虽然 Windows 容器中的进程比 Linux 容器要多，但与常见的 Windows 服务器相比，其进程数量却是明显偏少的。

按 Ctrl-PQ 组合键，可以在退出容器的同时还保持容器运行。这样 Shell 就会返回到 Docker 主机终端。可以通过查看 Shell 提示符来确认。

返回到 Docker 主机的 Shell 提示符，再次运行`ps`命令。

Linux 示例如下。

$ ps -elf
F S UID PID PPID NI ADDR SZ WCHAN TIME CMD
4 S root 1 0 0 - 9407 - 00:00:03 /sbin/init
1 S root 2 0 0 - 0 - 00:00:00 [kthreadd]
1 S root 3 2 0 - 0 - 00:00:00 [ksoftirqd/0]
1 S root 5 2 -20 0 - 00:00:00 [kworker/0:0H]
1 S root 7 2 -0 - 0 - 00:00:00 [rcu_sched]
<Snip>
0 R ubuntu 22783 22475 0 - 9021 - 00:00:00 ps -elf

Windows 示例如下。

> ps
Handles NPM(K) PM(K) WS(K) CPU(s) Id SI ProcessName
------- ------ ----- ----- ------ -- -- -----------
220 11 7396 7872 0.33 1732 0 amazon-ssm-agen
84 5 908 2096 0.00 2428 3 CExecSvc
87 5 936 1336 0.00 4716 4 CExecSvc
203 13 3600 13132 2.53 3192 2 conhost
210 13 3768 22948 0.08 5260 2 conhost
257 11 1808 992 0.64 524 0 csrss
116 8 1348 580 0.08 592 1 csrss
85 5 532 1136 0.23 2440 3 csrss
242 11 1848 952 0.42 2708 2 csrss
95 5 592 980 0.00 4524 4 csrss
137 9 7784 6776 0.05 5080 2 docker
401 17 22744 14016 28.59 1748 0 dockerd
307 18 13344 1628 0.17 936 1 dwm
<SNIP>
1888 0 128 136 37.17 4 0 System
272 15 3372 2452 0.23 3340 2 TabTip
72 7 1184 8 0.00 3400 2 TabTip32
244 16 2676 3148 0.06 1880 2 taskhostw
142 7 6172 6680 0.78 4952 3 WmiPrvSE
148 8 5620 11028 0.77 5568 4 WmiPrvSE

可以看到与容器相比，Docker 主机中运行的进程数要多很多。Windows 容器中运行的进程要远少于 Windows 主机，Linux 容器中的进程数也远少于 Linux 主机。

在之前的步骤当中，是使用 Ctrl-PQ 组合键来退出容器的。在容器内部使用该操作可以退出当前容器，但不会杀死容器进程。

可以通过`docker container ls`命令查看系统内全部处于运行状态的容器。

$ docker container ls
CONTAINER ID IMAGE COMMAND CREATED STATUS NAMES
e2b69eeb55cb ubuntu:latest "/bin/bash" 7 mins Up 7 min vigilant_borg

上述的输出显示只有一个运行中的容器。这就是前面示例中创建的那个容器。输出中有该容器，证明了容器在退出后依然是运行的。可以看到这个进程是 7min 之前创建的，并且一直在运行。

连接到运行中的容器

执行`docker container exec`命令，可以将 Shell 连接到一个运行中的容器终端。因为之前示例中的容器仍在运行，所以下面的示例会创建到该容器的新连接。

Linux 示例如下。

$ docker container exec -it vigilant_borg bash
root@e2b69eeb55cb:/#

示例中的容器名为“vigilant_borg”。读者环境中的容器名称会不同，所以请记得将“vigilant_borg”替换为自己 Docker 主机上运行中的容器名称或者 ID。

Windows 示例如下。

> docker container exec -it pensive_hamilton pwsh.exe

Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.
PS C:\>

本例中使用的容器为“pensive_hamilton”。同样，读者环境中的容器名称会不同，所以请记得将“pensive_hamilton”替换为自己 Docker 主机上运行中的容器名称或者 ID。

注意，Shell 提示符又发生了变化。此时已登录到了容器内部。

`docker container exec`命令的格式如下：

docker container exec <options> <container-name or container-id> <command/app>

在示例中，将本地 Shell 连接到容器是通过 -it 参数实现的。本例中使用名称引用容器，并且告诉 Docker 运行 Bash Shell（在 Windows 示例中是 PowerShell）。

使用十六进制 ID 的方式也可以很容易地引用具体容器。

再次使用 Ctrl-PQ 组合键退出容器。

Shell 提示符应当退回到 Docker 主机中。

再次运行 docker container ls 命令来确认容器仍处于运行状态。

$ docker container ls
CONTAINER ID IMAGE COMMAND CREATED STATUS NAMES
e2b69eeb55cb ubuntu:latest "/bin/bash" 9 mins Up 9 min vigilant_borg

通过`docker container stop`和`docker container rm`命令来停止并杀死容器。切记需要将示例中的名称 /ID 替换为读者自己的容器对应的名称和 ID。

$ docker container stop vigilant_borg
vigilant_borg

$ docker container rm vigilant_borg
vigilant_borg

通过运行`docker container ls`命令，并指定 -a 参数来确认容器已经被成功删除。添加 -a 的作用是让 Docker 列出所有容器，甚至包括那些处于停止状态的。

$ docker container ls -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES