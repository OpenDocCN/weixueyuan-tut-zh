# RegionServer 是什么？

> 原文：[`c.biancheng.net/view/6513.html`](http://c.biancheng.net/view/6513.html)

在 HDFS 中，DataNode 负责存储实际数据。RegionServer 主要负责响应用户的请求，向 HDFS 读写数据。一般在分布式集群中，RegionServer 运行在 DataNode 服务器上，实现数据的本地性。

每个 RegionServer 包含多个 Region，它负责的功能如下：

*   处理分批给它的 Region。
*   处理客户端读写请求。
*   刷新缓存到 HDFS 中。
*   处理 Region 分片。
*   执行压缩。

RegionServer 是 HBase 中最核心的模块，其内部管理了一系列 Region 对象，每个 Region 由多个 HStore 组成，每个 HStore 对应表中一个列族的存储。

HBase 是按列进行存储的，将列族作为一个集中的存储单元，并且 HBase 将具备相同 I/O 特性的列存储到一个列族中，这样可以保证读写的高效性。

![](img/4650b9fc8a86521cffb9dcbcfbcd262e.png)在上图中，RegionServer 最终将 Region 数据存储在 HDFS 中，采用 HDFS 作为底层存储。

HBase 自身并不具备数据复制和维护数据副本的功能，而依赖 HDFS 为 HBase 提供可靠和稳定的存储。

当然，HBase 也可以不采用 HDFS，如它可以使用本地文件系统或云计算环境中的 Amazon S3。本专题中 HBase 的内容都是以 HDFS 为底层存储来描述的。