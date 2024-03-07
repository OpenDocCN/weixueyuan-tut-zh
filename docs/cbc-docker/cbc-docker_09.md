# Docker Storage Driver：存储驱动

> 原文：[`c.biancheng.net/view/3130.html`](http://c.biancheng.net/view/3130.html)

每个 Docker 容器都有一个本地存储空间，用于保存层叠的镜像层（Image Layer）以及挂载的容器文件系统。

默认情况下，容器的所有读写操作都发生在其镜像层上或挂载的文件系统中，所以存储是每个容器的性能和稳定性不可或缺的一个环节。

以往，本地存储是通过存储驱动（Storage Driver）进行管理的，有时候也被称为 Graph Driver 或者 GraphDriver。

虽然存储驱动在上层抽象设计中都采用了栈式镜像层存储和写时复制（Copy-on-Write）的设计思想，但是 Docker 在 Linux 底层支持几种不同的存储驱动的具体实现，每一种实现方式都采用不同方法实现了镜像层和写时复制。

虽然底层实现的差异不影响用户与 Docker 之间的交互，但是对 Docker 的性能和稳定性至关重要。

在 Linux 上，Docker 可选择的一些存储驱动包括 AUFS（最原始也是最老的）、Overlay2（可能是未来的最佳选择）、Device Mapper、Btrfs 和 ZFS。

Docker 在 Windows 操作系统上只支持一种存储驱动，即 Windows Filter。

存储驱动的选择是节点级别的。这意味着每个 Docker 主机只能选择一种存储驱动，而不能为每个容器选择不同的存储驱动。

在 Linux 上，读者可以通过修改 /etc/docker/daemon.json 文件来修改存储引擎配置，修改完成之后需要重启 Docker 才能够生效。

下面的代码片段展示了如何将存储驱动设置为 overlay2。

{ "storage-driver": "overlay2" }

> 提示：如果配置所在行不是文件的最后一行，则需要在行尾处增加逗号。

如果读者修改了正在运行 Docker 主机的存储引擎类型，则现有的镜像和容器在重启之后将不可用，这是因为每种存储驱动在主机上存储镜像层的位置是不同的（通常在 /var/lib/docker/ <storage-driver>/... 目录下）。

修改了存储驱动的类型，Docker 就无法找到原有的镜像和容器了。切换到原来的存储驱动，之前的镜像和容器就可以继续使用了。

如果希望在切换存储引擎之后还能够继续使用之前的镜像和容器，需要将镜像保存为 Docker 格式，上传到某个镜像仓库，修改本地 Docker 存储引擎并重启，之后从镜像仓库将镜像拉取到本地，最后重启容器。

通过下面的命令来检查 Docker 当前的存储驱动类型。

$ docker system info
<Snip>
Storage Driver: overlay2
Backing Filesystem: xfs
Supports d_type: true
Native Overlay Diff: true
<Snip>

选择存储驱动并正确地配置在 Docker 环境中是一件重要的事情，特别是在生产环境中。

下面的清单可以作为一个参考指南，帮助我们选择合适的存储驱动。同时还可以参阅 Docker 官网上由 Linux 发行商提供的最新文档来做出选择。

*   Red Hat Enterprise Linux：4.x 版本内核或更高版本 + Docker 17.06 版本或更高版本，建议使用 Overlay2。
*   Red Hat Enterprise Linux：低版本内核或低版本的 Docker，建议使用 Device Mapper。
*   Ubuntu Linux：4.x 版本内核或更高版本，建议使用 Overlay2。
*   Ubuntu Linux：更早的版本建议使用 AUFS。
*   SUSE Linux Enterprise Server：Btrfs。

我们需要时刻关注 Docker 文档中关于存储驱动的最新支持和版本兼容列表。尤其是正在使用 Docker 企业版（EE），并且有售后支持合同的情况下，更有必要查阅最新文档。

## Device Mapper 配置

大部分 Linux 存储驱动不需要或需要很少的配置。但是，Device Mapper 通常需要合理配置之后才能表现出良好的性能。

默认情况下，Device Mapper 采用 loopback mounted sparse file 作为底层实现来为 Docker 提供存储支持。

如果需要的是开箱即用并且对性能没什么要求，那么这种方式是可行的。但这并不适用于生产环境。实际上，默认方式的性能很差，并不支持生产环境。

为了达到 Device Mapper 在生产环境中的最佳性能，读者需要将底层实现修改为 direct-lvm 模式。

这种模式下通过使用基于裸块设备（Raw Block Device）的 LVM 精简池（LVM thin pool）来获取更好的性能。

在 Docker 17.06 以及更高的版本中可以配置 direct-lvm 作为存储驱动。

其中最主要的一点是，这种方式只能配置一个块设备，并且只有在第一次安装后才能设置生效。未来可能会有改进，但就目前情况来看配置单一块设备这种方式在性能和可靠性上都有一定的风险。

## 让 Docker 自动设置 direct-lvm

下面的步骤会将 Docker 配置存储驱动为 Device Mapper，并使用 direct-lvm 模式。

1) 将下面的存储驱动配置添加到 /etc/docker/daemon.json 当中。

{
"storage-driver": "devicemapper",
"storage-opts": [
        "dm.directlvm_device=/dev/xdf",
        "dm.thinp_percent=95",
        "dm.thinp_metapercent=1",
        "dm.thinp_autoextend_threshold=80",
        "dm.thinp_autoextend_percent=20",
        "dm.directlvm_device_force=false"
    ]
}

Device Mapper 和 LVM 是很复杂的知识点，下面简单介绍一下各配置项的含义。

*   dm.directlvm_device：设置了块设备的位置。为了存储的最佳性能以及可用性，块设备应当位于高性能存储设备（如本地 SSD）或者外部 RAID 存储阵列之上。
*   dm.thinp_percent=95：设置了镜像和容器允许使用的最大存储空间占比，默认是 95%。
*   dm.thinp_metapercent：设置了元数据存储（MetaData Storage）允许使用的存储空间大小。默认是 1%。
*   dm.thinp_autoextend_threshold：设置了 LVM 自动扩展精简池的阈值，默认是 80%。
*   dm.thinp_autoextend_percent：表示当触发精简池（thin pool）自动扩容机制的时候，扩容的大小应当占现有空间的比例。
*   dm.directlvm_device_force：允许用户决定是否将块设备格式化为新的文件系统。

2) 重启 Docker。

3) 确认 Docker 已成功运行，并且块设备配置已被成功加载。

$ docker version
Client:
Version: 18.01.0-ce
<Snip>
Server:
Version: 18.01.0-ce
<Snip>

$ docker system info
<Snipped output only showing relevant data>
Storage Driver: devicemapper
Pool Name: docker-thinpool
Pool Blocksize: 524.3 kB
Base Device Size: 25 GB
Backing Filesystem: xfs
Data file: << Would show a loop file if in loopback mode
Metadata file: << Would show a loop file if in loopback mode
Data Space Used: 1.9 GB
Data Space Total: 23.75 GB
Data Space Available: 21.5 GB
Metadata Space Used: 180.5 kB
Metadata Space Total: 250 MB
Metadata Space Available: 250 MB

即使 Docker 在 direct-lvm 模式下只能设置单一块设备，其性能也会显著优于 loopback 模式。

## 手动配置 Device Mapper 的 direct-lvm

#### 1) 块设备（Block Device）

在使用 direct-lvm 模式的时候，读者需要有可用的块设备。这些块设备应该位于高性能的存储设备之上，比如本地 SSD 或者外部高性能 LUN 存储。

如果 Docker 环境部署在企业私有云（On-Premise）之上，那么外部 LUN 存储可以使用 FC、iSCSI，或者其他支持块设备协议的存储阵列。

如果 Docker 环境部署在公有云之上，那么可以采用公有云厂商提供的任何高性能的块设备（通常基于 SSD）。

#### 2) LVM 配置

Docker 的 Device Mapper 存储驱动底层利用 LVM（Logical Volume Manager）来实现，因此需要配置 LVM 所需的物理设备、卷组、逻辑卷和精简池。

读者应当使用专用的物理卷并将其配置在相同的卷组当中。这个卷组不应当被 Docker 之外的工作负载所使用。

此外还需要配置额外两个逻辑卷，分别用于存储数据和源数据信息。另外，要创建 LVM 配置文件、指定 LVM 自动扩容的触发阈值，以及自动扩容的大小，并且为自动扩容配置相应的监控，保证自动扩容会被触发。

#### 3) Docker 配置

修改 Docker 配置文件之前要先保存原始文件（etc/docker/daemon.json），然后再进行修改。

环境中的 dm.thinpooldev 配置项对应值可能跟下面的示例内容有所不同，需要修改为合适的配置。

{
    "storage-driver": "devicemapper",
    "storage-opts": [
        "dm.thinpooldev=/dev/mapper/docker-thinpool",
        "dm.use_deferred_removal=true",
        "dm.use_deferred_deletion=true"
    ]
}

修改并保存配置后，读者可以重启 Docker daemon。