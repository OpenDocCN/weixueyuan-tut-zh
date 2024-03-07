# Windows Server 安装 Docker

> 原文：[`c.biancheng.net/view/3126.html`](http://c.biancheng.net/view/3126.html)

下面我们主要介绍在 Windows Servre 2016 上安装 Docker 的方法。

主要包括以下步骤。

1) 安装 Windows 容器功能（Windows Container Feature）。

2) 安装 Docker。

3) 确认安装成功。

在开始安装之前，需要确保操作系统已经更新了最新版本的包以及安全补丁。

可以通过运行 sconfig 命令，并选择选项 6 来快速完成更新的安装。安装更新可能需要重启系统。

接下来将在没有安装容器功能（Container Feature）或者已经安装了老版本 Docker 的 Windows Server 2016 上进行演示。

确保容器特性已经安装并且启用。

1) 鼠标右击 Windows 开始按钮，选择“应用和功能”，接下来会打开“应用和功能”面板。

2) 单击“启用或关闭 Windows 功能”，接下来会打开“服务器管理器”。

3) 确认面板处于选中状态，然后选择“添加角色和功能”。

4) 根据向导提示执行，直到进入“功能”页面。

5) 确保“容器”功能已经勾选，然后单击向导的“完成”按钮。完成之后需要重启操作系统。

现在已经完成 Windows 容器功能的安装，接下来可以安装 Docker 了。

我们将采用 PowerShell 完成安装。

1) 以管理员身份运行 PowerShell。

2) 运行下面的命令来安装 Docker 包管理工具。

> Install-Module DockerProvider -Force

如果出现提示，单击允许（Accept）按钮完成 NuGet provider 的安装。

3) 安装 Docker。

> Install-Package Docker -ProviderName DockerProvider -Force

一旦安装完成，读者可以看到下面的内容。

Name Version Source Summary
---- ------- ------ -------
Docker 17.06.2-ee-6 Docker Docker for Windows Server 2016

现在 Docker 已经完成安装，并且设置为开机自启动。

4) 通过重启系统来确认 Docker 的安装没有对系统启动造成任何影响。此外在重启之后，可以检查 Docker 是否自动启动。

Docker 现在已经安装成功，可以开始部署容器了。

下面的命令是确认 Docker 安装成功的方法。

> docker --version
Docker version 17.06.2-ee-6, build e75fdb8

> docker system info
Containers: 0
Running: 0
Paused: 0
Stopped: 0
Images: 0
Server Version: 17.06.2-ee-6
Storage Driver: windowsfilter
<Snip>

Docker 现在已经完成安装，读者可以开始运行 Windows 容器了。