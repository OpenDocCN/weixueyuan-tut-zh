# 常见开源操作系统有哪些？

> 原文：[`c.biancheng.net/view/3852.html`](http://c.biancheng.net/view/3852.html)

开源的意思是系统的源代码是面向用户开放的，可以遵循开源协议（GNU）进行使用、编译和再发布。在遵守 GNU 协议的前提下，任何人都可以免费使用，随意控制软件的运行方式。最著名的开源操作系统是 linux。

开源系统相对于不开源的操作系统，如 Windows 和 Mac，开源操作系统最大的特点就是开放源代码和自由定制，但也会因为使用者的技术水平等关系出现很多不可预知的情况及维护问题，并且由于多数硬件和软件厂商并不支持开源软件。所以在驱动和软件源方面有很大困难，这就要求个人用户在选择系统时需要注意根据自己的实际情况来选择，这也是目前个人使用开源操作系统较为困难的最主要原因。

开源系统的分类：

*   Unix-like 类：Linux（这一族开发者众多，发行版本以千计，覆盖所有平台，并支持所有文件格式和所有网络协议）、FreeBSD、OpenBSD、NetBSD（覆盖所有平台）、DrangonflyBSD、Darwin/OpenDarwin/PureDarwin、Minix、GNU Mach/GNU Hurd、L4/Fiasco/Pistachio、syllable、禅 OS
*   Unix 类：Opensolaris、AuroraUX、Plan9、Inferno-Plan 9。
*   dos 类：Freedos
*   windows-like 类：ReactOS
*   原创类：House、KolibriOS、MenuetOS、HelenOS、Openvms
*   还有其他的开源操作系统有：XFree86、ReactOS。

#### 1）Linux 内核

Linux 内核诞生于 1991 年，目前已超过 1500 万行代码，价值达数百亿美元。以它为核心推出的操作系统发行版最为流行的有 RedHat、Suse、Ubuntu、Android、Centos、Debian 等，大概有上百种（具体可参考排行网站 [http：//distrowatch.com](http://distrowatch.com)）。如果不特别指定，这些发行版统称为 Linux 操作系统。

Linux 操作系统开源免费、稳定可靠、病毒少、性能高，作为平台软件，被广泛应用在云端、数据中心。目前除微软外，全部云中心都采用 Linux 操作系统。CentOS 是 RedHat 的克隆版，应用比较广泛，网上具有丰富的资料和社区人气，现已被红帽收购。

#### 2）FreeBSD

FreeBSD 是由美国加州大学伯克利分校开发的免费开源的操作系统，号称世界上最稳定、最高效、最安全、版权限制最少的操作系统。其他公司或者个人可以下载源代码，进行修改后不开源，比如一些网络设备生产商修改源代码后集成到自己的产品中，修改后的代码不开源是不违法的，这点与 Linux 操作系统不同。

FreeBSD 是最理想的网络服务器操作系统之一，可以从 [http：//www.freebsd.org/zh_CN/where.html](http://www.freebsd.org/zh_CN/where.html) 网站下载。鼎鼎有名的开源 NAS 项目 FreeNAS 就是采用了这个操作系统。

#### 3）Illumos 内核

Oracle 收购 Sun 公司之后，关闭了 OpenSolaris 开源项目，推出闭源的 Solaris 11 操作系统和一个简化版的 Solaris 11 Express。Solaris 11 可以从官方网站下载，如果商用，就需要购买服务，个人和实验室可以免费使用。

OpenSolaris 的原班人马几乎都离开了 Oracle 公司，分别加入了以 illumos 为核心的各个厂商，一时间发布了许多个开源版本，如 Napp-it to GO、NexentaStor、OmniOS、SmartOS、StormOS、illumian、OpenIndiana，可分别应用于后台服务器、桌面计算机、存储、嵌入式等领域。这些发行版都以 illumos 为核心，这一点类似于各种 Linux 发行版，如红帽、乌班图、Debian 等 Linux 操作系统发行版都是以 Linux 为核心的。

利用 Napp-it to GO 自己搭建 NAS 存储是一个低成本的存储解决方案，其中核心是 ZFS 文件系统。但是开源的各个操作系统版本中 ZFS 文件系统只到 2.8 版本，更高的版本被 Oracle 公司闭源了，Oracle 公司现在已经推出了 ZFS 3.4 版本。如果自己使用，则可以采用 Solaris 11+Napp-it 组件企业级的 NAS 存储系统。不过 Napp-it 的界面太过复杂，用户体验非常糟糕。