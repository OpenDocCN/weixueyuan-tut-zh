# Linux ifconfig 命令：管理 IP 地址

> 原文：[`www.weixueyuan.net/a/152.html`](http://www.weixueyuan.net/a/152.html)

Linux ipconfig 命令用于查看和配置网络接口的地址和参数，包括 IP 地址、网络掩码、广播地址，它的使用权限是超级用户，其有两种使用格式，分别用于查看和更改网络接口。

ifconfig [选项] [网络接口]：用来查看当前系统的网络配置情况。
ifconfig 网络接口 [选项] 地址：用来配置指定接口（如 ehh0、eth1）的 IP 地址、网络掩码、广播地址等。

ifconfig 第二种使用方式的常见选项说明如表 1 所示。

表 1：ifconfig 命令选项说明

| 选项 | 说明 |
| --- | --- |
| -interface | 指定的网络接口名，如 eth0 和 eth1 |
| up | 激活指定的网络接口卡 |
| down | 关闭指定的网络接口卡 |
| broadcast address | 设置接口的广播地址 |
| poin to point | 启用点对点方式 |
| address | 设置指定接口设备的 IP 地址 |
| netmask address | 设置接口的子网掩码地址 |