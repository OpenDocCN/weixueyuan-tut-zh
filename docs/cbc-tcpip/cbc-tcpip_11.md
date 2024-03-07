# netwox 显示网络配置信息

> 原文：[`c.biancheng.net/view/6384.html`](http://c.biancheng.net/view/6384.html)

计算机的网络配置信息包含网络设备接口、IP 地址、MAC 地址和掩码等信息。为了方便用户查看计算机中的这些信息，netwox 工具提供了对应的模块，用于获取网络配置信息。

## 显示网络配置信息

为了了解当前网络的相关信息，netwox 工具提供了编号为 1 的模块。它可以显示当前主机的网络接口信息、主机的 IP 地址信息，以及路由表等信息。

显示网络配置信息，执行命令如下：

root@daxueba:~# netwox 1

执行命令后将显示当前网络设备信息。

由于信息较多，下面对每个部分进行讲解。

1) 显示网络设备接口列表信息如下：

############################## Devices ###############################
nu      dev         ethernet_hwtype         mtu         real_device_name
1        Lo0         loopback                     65536      lo
2        Eth0        00:0C:29:CA:E4:66       1500        eth0

以上输出信息中每列含义如下：

*   nu：设备编号。
*   dev：设备接口名称的简单形式。
*   ethernet_hwtype：以太网地址或硬件类型。
*   mtu：MTU 值。
*   real_device_name：设备接口名称的真正形式。

2) 显示 IP 地址列表信息如下：

################################# IP #################################
nu       ip                           /netmask                          ppp point_to_point_with
1        127.0.0.1                 /255.0.0.0                              0
1        ::1/128                                                                  0
2        192.168.59.131       /255.255.255.0                      0
2        fd15:4ba5:5a2b:1008:20c:29ff:feca:e466/64        0
2        fe80::20c:29ff:feca:e466/64                                  0
2        fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a/64     0

以上输出信息中每列含义如下：

*   nu：与此地址关联的设备编号。
*   ip：IP 地址。
*   netmask：子网掩码。
*   ppp：点对点的地址。
*   point_to_point_with：远程端点的地址。

3) IP4 ARP 缓存或 IP6 邻居信息如下：

######################### ArpCache/Neighbor ##########################
nu       ethernet                                    ip
2        00:0C:29:C4:8A:DE              192.168.59.132
2        00:0C:29:CA:E4:66               192.168.59.131
2        00:0C:29:CA:E4:66               fd15:4ba5:5a2b:1008:20c:29ff:feca:e466
2        00:0C:29:CA:E4:66               fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a
2        00:0C:29:CA:E4:66               fe80::20c:29ff:feca:e466
2        00:50:56:EA:F3:A1               192.168.59.2
2        00:50:56:EA:F3:A1               fe80::250:56ff:fec0:2222
2        00:50:56:F0:69:32               192.168.59.254

以上输出信息中每列含义如下：

*   nu：与此条目关联的设备编号。
*   ethernet：计算机的以太网地址。
*   ip：计算机的 IP 地址。

4) 显示路由信息如下：

############################## Routes ################################
nu       destination             /netmask               source                gateway           metric
1         127.0.0.1               /255.255.255.255       local                  0
2         192.168.59.131     /255.255.255.255       local                  0
2         192.168.59.0         /255.255.255.0          192.168.59.131  100
1         127.0.0.0               /255.0.0.0                  127.0.0.1            0
2          0.0.0.0                  /0.0.0.0                      192.168.59.131  192.168.59.2   100
1         ::1/128                  local                            0
2         fd15:4ba5:5a2b:1008:20c:29ff:feca:e466/128    local              0
2         fe80::20c:29ff:feca:e466/128                              local              0
2         fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a/128 local              0
2         fd15:4ba5:5a2b:1008::/64   fd15:4ba5:5a2b:1008:20c:29ff:feca:e466    0
2         fe80::/64             fe80::20c:29ff:feca:e466                                               0
2         fd15:4ba5:5a2b:1008::/64  fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a  0
2         fd15:4ba5:5a2b:1008::/64  fd15:4ba5:5a2b:1008:20c:29ff:feca:e466    fe80::250:56ff:fec0:2222 100
2          ::/0                     fe80::20c:29ff:feca:e466 fe80::250:56ff:fec0:2222 100

以上输出信息中每列含义如下：

*   nu：与此条目关联的设备编号。
*   destination：目标地址。
*   netmask：掩码。
*   source：源 IP 地址或本地路由。
*   gateway：网关。
*   metric：路线度量。

## 显示网络调试信息

如果想了解更多的网络信息，netwox 工具还提供了编号为 2 的模块，用于显示网络调试信息。

显示网络调试信息，执行命令如下：

root@daxueba:~# netwox 2

执行命令后可以看到，不仅显示了网络配置信息，还显示了调试信息：

Netwox toolbox version 5.39.0.   #版本信息
Netwib library version 5.39.0.

####****####****####****####****####
NETWIBDEF_SYSNAME="Linux"
NETWIBDEF_SYSARCH="amd64"
NETWIBDEF_ARCH_ENDIAN=0
NETWIBDEF_ARCH_BITS=64
NETWIBDEF_ARCH_ALIGN=1
#省略其他信息
NETWIBDEF_HAVEVAR_SC_GETPW_R_SIZE_MAX=1
NETWIBDEF_HAVEVAR_SC_GETGR_R_SIZE_MAX=1
Error 0 : ok  #0 个错误

####****####****####****####****####
############################## Devices ################################
nu       dev         ethernet_hwtype         mtu         real_device_name
1         Lo0          loopback                    65536       lo
2         Eth0         00:0C:29:CA:E4:66      1500         eth0
################################# IP ##################################
nu       ip                       /netmask                          ppp point_to_point_with
1        127.0.0.1             /255.0.0.0                                0
1        ::1/128                                                                0
2        192.168.59.131  /255.255.255.0                         0
2        fd15:4ba5:5a2b:1008:20c:29ff:feca:e466/64      0
2        fe80::20c:29ff:feca:e466/64                                0
2        fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a/64   0
########################## ArpCache/Neighbor ##########################
nu       ethernet                          ip
2        00:0C:29:C4:8A:DE         192.168.59.132
2        00:0C:29:CA:E4:66          192.168.59.131
2        00:0C:29:CA:E4:66           fd15:4ba5:5a2b:1008:20c:29ff:feca:e466
2        00:0C:29:CA:E4:66           fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a
2        00:0C:29:CA:E4:66           fe80::20c:29ff:feca:e466
2        00:50:56:EA:F3:A1           192.168.59.2
2        00:50:56:EA:F3:A1           fe80::250:56ff:fec0:2222
2        00:50:56:F0:69:32           192.168.59.254
############################## Routes #################################
nu      destination          /netmask                 source        gateway                      metric
1        127.0.0.1             /255.255.255.255      local                       0
2        192.168.59.131   /255.255.255.255      local                       0
2        192.168.59.0      /255.255.255.0          192.168.59.131       100
1        127.0.0.0           /255.0.0.0                   127.0.0.1                 0
2        0.0.0.0               /0.0.0.0                       192.168.59.131       192.168.59.2    100
1        ::1/128                                                  local                       0
2        fd15:4ba5:5a2b:1008:20c:29ff:feca:e466/128  local           0
2        fe80::20c:29ff:feca:e466/128                local                       0
2        fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a/128 local         0
2        fd15:4ba5:5a2b:1008::/64  fd15:4ba5:5a2b:1008:20c:29ff:feca:e466          0
2        fe80::/64      fe80::20c:29ff:feca:e466                                  0
2        fd15:4ba5:5a2b:1008::/64  fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a       0
2        fd15:4ba5:5a2b:1008::/64  fd15:4ba5:5a2b:1008:20c:29ff:feca:e466
fe80::250:56ff:fec0:2222 100
2        ::/0  fe80::20c:29ff:feca:e466 fe80::250:56ff:fec0:2222        100
Error 0 : ok
hint: errno = 19 = No such device
hint: this is not an IPv4 address: fe80::250:56ff:fec0:2222

####****####****####****####****####

:::: After devices_ioctl ::::  # devices_ioctl 信息
$$$ devices $$$
d=0,lo, m=65536 t=loopback
d=0,eth0, m=1500 t=ethernet>00:0C:29:CA:E4:66
d=0,lo, m=65536 t=loopback
d=0,eth0, m=1500 t=ethernet>00:0C:29:CA:E4:66

$$$ ip $$$
d=0,lo i=127.0.0.1/255.0.0.0 p=false
d=0,eth0 i=192.168.59.131/255.255.255.0 p=false

$$$ arpcache $$$

$$$ routes $$$

:::: After procnetifinet6 ::::  # procnetifinet6 信息
$$$ devices $$$
d=0,lo, m=65536 t=loopback
d=0,eth0, m=1500 t=ethernet>00:0C:29:CA:E4:66
d=0,lo, m=65536 t=loopback
d=0,eth0, m=1500 t=ethernet>00:0C:29:CA:E4:66

$$$ ip $$$
d=0,lo i=127.0.0.1/255.0.0.0 p=false
d=0,eth0 i=192.168.59.131/255.255.255.0 p=false
d=0,lo i=::1/128 p=false
d=0,eth0 i=fd15:4ba5:5a2b:1008:20c:29ff:feca:e466/64 p=false
d=0,eth0 i=fe80::20c:29ff:feca:e466/64 p=false
d=0,eth0 i=fd15:4ba5:5a2b:1008:4c3c:fda9:c3dc:499a/64 p=false
…  #省略其他信息
$$$ routes $$$
d=1,lo i=127.0.0.1/255.255.255.255 s=false g=false m=0
d=2,eth0 i=192.168.59.131/255.255.255.255 s=false g=false m=0
d=2,eth0 i=192.168.59.0/255.255.255.0 s=true,192.168.59.131 g=false m=100
d=1,lo i=127.0.0.0/255.0.0.0 s=true,127.0.0.1 g=false m=0
d=2,eth0 i=::/0 s=true,fe80::20c:29ff:feca:e466 g=true,fe80::250:56ff:
fec0:2222 m=100
Error 0 : ok
hint: errno = 19 = No such device
hint: this is not an IPv4 address: fe80::250:56ff:fec0:2222

####****####****####****####****####
END