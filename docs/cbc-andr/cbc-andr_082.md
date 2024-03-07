# WIFI Direct（WIFI 直连）是什么？

> 原文：[`c.biancheng.net/view/3190.html`](http://c.biancheng.net/view/3190.html)

WIFI Direct 意为通过 WIFI 直接建立连接。

2010 年 10 月，Wi-Fi Alliance（WIFI 联盟）发布 WIFI Direct 白皮书，白皮书中介绍了关于这种技术的基本信息、特点和功能。

WIFI Direct 标准是指允许无线网络中的设备无须通过无线路由器即可相互连接。这种标准支持 WIFI 的无线设备像蓝牙那样以点对点的形式互连，但是在传输速度与传输距离方面都比蓝牙有大幅提升。

WIFI Direct 设备是支持对等连接的设备，这种设备既支持基础设施网络，也支持 P2P（Peer To Peer，点对点）连接。

Android N 提供了 WIFI Direct 用于 WIFI 的直接连接。借助于 WIFI Direct API，支持 WIFI 功能的 Android N 系统的手机可以直接通过 WIFI 连接，而不需要经过接入点。

WIFI Direct 提供 WifiP2pManager 类，其功能主要分为以下三部分：

*   WifiP2pManager 类提供相关 API 用于发现可连接的点，并进行请求和建立连接。
*   每个 WifiP2pManager 的方法都要求传入对应的监听器，用于监听对该方法是否成功运行。
*   当检测到特定事件，如可连接的点减少或者发现了新的可连接的点，WIFI Direct 框架会通过 Intent 通知用户。

一般情况下，这三部分功能是共同使用的。例如，可以通过 WifiP2pManager.ActionListener 调用 discoverPeers()，以便当建立连接时，可以通过 ActionListener.onSuccess() 和 ActionListener.onFailure() 方法获得相应结果的通知。

当 discoverPeers() 方法探测到发现列表中的点发生改变时，一个包含 WIFI_P2P_PEERS_CHANGED_ACTION 信息的 Intent 会被广播。WifiP2pManager 提供的方法如表 1 所示。

**表 1 WifiP2pManager 的方法**

| 方法名 | 描述 |
| --- | --- |
| initialize() | 为应用程序注册 WIFI 框架。该方法必须在任何其他 WIFI Direct 方法被调用前调用 |
| connect() | 与具有指定配置的 WIFI 设备建立点对点连接 |
| cancelConnect() | 断开连接 |
| requestConnectInfo() | 获取设备的连接信息 |
| createGroup() | 以当前设备为拥有者创建一个点对点组 |
| removeGroup() | 删除当前的点对点组 |
| requestGroupInfo() | 获取点对点组的信息 |
| discoverPeers() | 初始化发现对等点设备服务 |
| requestPeers() | 获取当前已发现的对等点设备列表 |

WifiP2pManager 支持的监听器如表 2 所示

**表 2 WifiP2pManager 支持的监听器**

| 监听器接口 | 相关动作 |
| --- | --- |
| WifiP2pManager.ActionListener | connect(), cancelConnect(), createGroup(), removeGroup(), and discoverPeers() |
| WifiP2pManager.ChannelListener | initialize() |
| WifiP2pManager.ConnectionInfoListener | requestConnectInfo() |
| WifiP2pManager.GroupInfoListener | requestGroupInfo() |
| WifiP2pManager.PeerListListener | requestPeers() |

WifiP2pManager 支持的 Intent 如表 3 所示。

**表 3 WifiP2pManager 支持的 Intent**

| Intent | 描述 |
| --- | --- |
| WIFI_P2P_CONNECTION_CHANGED_ACTION | 当 WIFI 设备的连接状态改变时广播 |
| WIFI_P2P_PEERS_CHANGED_ACTION | 当 discoverPeers() 方法被调用时广播。通过该 Intent 可以获取  到最新的对等点设备的列表 |
| WIFI_P2P_STATE_CHANGED_ACTION | 当 WIFI Direct 功能在设备上被打开或者关闭时广播 |
| WIFI_P2P_THIS_DEVICE_CHANGED_ACTION | 当 WIFI 设备的具体信息改变时广播，例如设备的名字改变时 |