# IP 地址是什么？

> 原文：[`c.biancheng.net/view/6385.html`](http://c.biancheng.net/view/6385.html)

IP 地址（Internet Protocol Address）是互联网协议特有的一种地址，它是 IP 协议提供的一种统一的地址格式。IP 地址为互联网上的每一个网络和每一台主机分配一个逻辑地址，以此来屏蔽物理地址的差异。

那么，我们为什么要使用 IP 地址呢？

在单个局域网网段中，计算机与计算机之间可以使用网络访问层提供的 MAC 地址进行通信。如果在路由式网络中，计算机之间进行通信就不能利用 MAC 地址实现数据传输了：因为 MAC 地址不能跨路由接口运行；即使强行实现跨越，使用 MAC 地址传输数据也是非常麻烦的。

这是由于内置在网卡里的固定 MAC 地址不能在地址空间上引入逻辑结构，使其无法具备真正的地址来表示国家、省、市、区、街道、路、号这类层次。因此，要进行数据传输，必须使用一种逻辑化、层次化的寻址方案对网络进行组织，这就是 IP 地址。

网络中的每个计算机都有对应的 IP 地址，读者可以使用 netwox 工具探测目标主机。

【示例】显示目标主机的相关信息。

1) 探测局域网中所有主机的 IP 地址、主机名和 MAC 地址信息。

root@daxueba:~# netwox 3 -a 192.168.59.0/24

显示的所有主机信息如下；

IP address:    192.168.59.0
Hostname:    localhost
Hostnames:    localhost
Eth address:    unresolved

IP address:      192.168.59.1  #主机 IP 地址
Hostname:      localhost
Hostnames:     localhost
Eth address:    00:50:56:C0:00:08  #MAC 地址

IP address:         192.168.59.2  #主机 IP 地址
Hostname:         localhost
Hostnames:       localhost
Eth address:       00:50:56:EA:F3:A1  #MAC 地址
…  #省略其他信息
IP address:        192.168.59.131  #主机 IP 地址
Hostname:        localhost
Hostnames:       localhost
Eth address:       00:0C:29:CA:E4:66  #MAC 地址

IP address:        192.168.59.132  #主机 IP 地址
Hostname:        localhost
Hostnames:       localhost
Eth address:       00:0C:29:C4:8A:DE  #MAC 地址
…  #省略其他信息
IP address:        192.168.59.254  #主机 IP 地址
Hostname:         localhost
Hostnames:       localhost
Eth address:       00:50:56:F7:32:70  #MAC 地址

IP address:        192.168.59.255  #主机 IP 地址
Hostname:        localhost
Hostnames:      localhost
Eth address:      unresolved

以上输出信息依次显示了局域网中的所有主机信息，主机 IP 地址为 192.168.59.0 到 192.168.59.255。

如果主机存在，就在 Eth address 部分中显示对应的 MAC 地址，如果主机不存在，则显示为 unresolved。从输出信息中可以了解到，主机 192.168.59.131 存在，其 MAC 地址为 00:0C:29:CA:E4:66。

2) 主机可以被用做服务器，探测域名为 www.163.com (http://www.163.com) 的所有主机的 IP 地址、主机名，以及 MAC 地址信息。

root@daxueba:~# netwox 3 -a www.163.com

输出信息如下：

IP address:        220.194.153.86
Hostname:       unresolved
Hostnames:      unresolved
Eth address:     unresolved

IP address:        218.26.75.208  #IPv4 地址
Hostname:        208.75.26.218.internet.sx.cn  #主机名
Hostnames:       208.75.26.218.internet.sx.cn
Eth address:       unresolved

IP address:        124.163.192.254  #IPv4 地址
Hostname:        254.192.163.124.adsl-pool.sx.cn  #主机名
Hostnames:       254.192.163.124.adsl-pool.sx.cn
Eth address:       unresolved

IP address:        2408:80f1:201:1::7  #IPv6 地址
Hostname:        unresolved
Hostnames:       unresolved
Eth address:      00:50:56:EA:F3:A1  #MAC 地址

IP address:        2408:80f1:201:1::6  #IPv6 地址
Hostname:        unresolved
Hostnames:       unresolved
Eth address:       00:50:56:EA:F3:A1  #MAC 地址

以上输出信息显示了域名 www.163.com（http://www.163.com）的主机所使用的 IP 地址、主机名，以及 MAC 地址信息。