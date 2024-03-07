# OceanBase 数据库入门教程

> 原文：[`c.biancheng.net/view/6588.html`](http://c.biancheng.net/view/6588.html)

OceanBase 是一款由阿里巴巴公司自主研发的高性能、分布式的关系型数据库，支持完整的 ACID 特性，高度兼容 MySQL 协议与语法，能够以最小的迁移成本使用高性能、可扩张、持续可用的分布式数据服务。

OceanBase 实现了数千亿条记录、数百 TB 数据的跨行跨表业务，支持了天猫大部分的 OLTP 和 OLAP 在线业务。

## OceanBase 特性

OceanBase 最初是为了处理淘宝网的大规模数据而产生的。传统的 Oracle 单机数据库无法支撑数百 TB 的数据存储、数十万的 QPS，通过硬件扩展的方式成本又太高。

淘宝网曾使用 MySQL 取代 Oracle，但是需要进行分库分表来存储，也有很多弊端。通过分库分表添加节点比较复杂，查询时有可能需要访问所有的分区数据库，性能很差。

淘宝网甚至考虑过 HBase，但是 HBase 只能支持单行事务查询，且不支持 ACID 特性，只支持最终一致性。而淘宝网的业务必须支持跨行跨表业务，且一些订单信息需要支持强一致性。

基于以上原因，这就需要开发一个新的数据库，既要有良好的可扩展性，又能支持跨行跨表事务，OceanBase 就应运而生了。

OceanBase 具有以下特性：

#### 1) 高扩展性

虽然传统关系型数据库（如 Oracle 或 MySQL）的功能已经很完善，但是数据库可扩展性比较差，随着数据量增大，需要进行分库分表存储，在查询时需要将相应的 SQL 解析到指定的数据库中，数据库管理员需要花费大量时间来做数据库扩容，且对维护人员的技术要求比较高，要掌握分布式处理中数据的读写分离、垂直拆分和水平拆分等技术。

而 OceanBase 使用分布式技术和无共享架构，数据自动分散到多台数据库主机上，采用廉价的 PC 服务器作为数据库主机，可以自由地对整个分布式数据库系统进行扩展，既降低了成本，同时也保证了无限的水平扩展。

OceanBase 也被称为云数据库，具有云存储的随意扩展的特性。

#### 2) 高可靠性

OceanBase 数据库系统使用的廉价的 PC 服务器，这些服务器是不可靠的，很容易出现故障。但是，OceanBase 又必须保证任何时刻出现的硬件故障不影响业务。

因此，OceanBase 引入 Paxos 协议，保证分布式事务的一致性，即数据库系统中数据以备份的方式存储于多台机器中，当其中一台出现故障时，其他备份仍可以使用，并根据系统日志来恢复故障前的数据。

#### 3) 数据准确性

OceanBase 是新型的关系型数据库，支持事务的 ACID 特性。这在电子商务、金融等领域是非常重要的，这些领域对数据的准确性要求非常高，如电子商务中的支付数据，这些数据要保持一致性，不能有任何数据的丢失。

OceanBase 在设计时，读事务基本是分布式并发执行的，而写事务则是集中式串行执行的，且任何一个写事务在最终提交前对其他读事务都是不可见的，因此 OceanbBase 是具有强一致性的，能保证数据的正确性。

#### 4) 高性能

数据库的总量是很大的，每天增、删、改的数据只是其中的小部分，这部分数据为增量数据。

OceanBase 将数据分成基准数据和增量数据，基准数据是保持不变的历史数据，用磁盘进行存储，可保证数据的稳定性；而增量数据是最近一段时间的修改数据，存储在内存中，这种针对增、删、 改记录的存储方式极大地提高了系统写事务的性能，并且增量数据在冻结后会转存到 SSD 上，仍然会提供较高性能的读服务。

OceanBase 会在系统的低负载时段对数据进行合并操作，避免对业务产生不良影响。

## OceanBase 系统架构

OceanBase 采用单台更新服务器来记录最近一段时间的修改增量，而基准数据以分布式文件系统的方式分散地存储于多台基准数据服务器中。

增、删、改事务集中在更新服务器上完成，避免了复杂的分布式事务，高效地实现了跨行跨表事务。而在进行数据查询时，需要把基准数据和增量数据融合后返回客户端。另外，更新服务器上的修改记录定期分发到多台基准数据服务器中，避免成为瓶颈，实现了良好的扩展性。

OceanBase 的系统架构如下图所示：

![OceanBase 系统架构](img/b0a0817f0a61de4869f949733c70eafc.png)
每个 OceanBase 集群包含客户端、RootServer、MergeServer、ChunkServer 以及 UpdateServer 几个部分，其中，RootServer 和 UpdateServer 一般部署在同一台物理机上，ChunkServer 和 MergeServer 部署在同一台物理机上，同时，集群由多个 ChunkServer/MergeServer 节点组成。

#### 1) 客户端

OceanBase 支持 JDBC 和客户端的访问，用户可通过客户端首先向 RootServer 发送请求，获取集群中 MergeServer 的地址列表，然后按照一定的策略选择一台 MergeServer 发送读写请求。

客户端选择 MergeServer 的策略有两种：

*   一种是随机的，从比较空闲的 MergeServer 中选取一台；
*   另一种则是采用一致性的散列法，一致性散列的策略将相同的 SQL 请求发送到同一台 MergeServer 上，这样可以方便 MergeServer 对查询结果进行缓存，减少从磁盘读取的次数。

#### 2) RootServer

RootServer 负责数据划分、集群服务器管理、负载均衡等操作。

在 OceanBase 集群中，表格中的数据按照主键进行排序和存储，主键由若干列组成，且具有唯一性。集群中的基准数据按照主键排序后划分成多个子表 Tablet，与 Bigtable 的定义类似，即将大数据块划分为小的数据块存储于不同的 ChunkServer 中。

每个 Tablet 的默认大小为 256MB， 并且在集群中每个 Tablet 默认包含三个副本，分布在多台 ChunkServer 中。RootServer 采用根表一级索引的结构，记录了每个 Tablet 所在的 ChunkServer。

RootServer 管理集群中所有的 MergeServer 和 ChunkServer 和 UpdateServer。RootServer 与 MergeServer 和 ChunkServer 之间保持心跳联系，能随时感知在线和已经下线的机器列表，当某台服务器（如 ChunkServer）下线时，RootServer 会触发对这台服务器上的 Tablet 增加副本的操作。

同时，RootServer 也会定期执行负载均衡，选择某些子表从负载较髙的机器迁移到负载较低的机器上。

RootServer 采用一主一备的结构，主备之间数据强同步，并通过 Linux  HA 软件实现高可用性。主备 RootServer 之间共享 VIP，VIP 为当前主 RootServer 的 IP。当主 RootServer 发生故障后，VIP 能够自动漂移到备 RootServer 所在的机器，备 RootServer 检测到以后切换为主 RootServer 并提供服务。

#### 3) ChunkServer

ChunkServer 负责存储集群的基准数据，OceanBase 将基准数据划分为大小约为 256MB 的 Tablet，每个 Tablet 由一个或者多个 SSTable 组成，每个 SSTable 由多个块组成。数据在 SSTable 中按照主键有序存储。

查找某一行数据时，需要首先定位这一行所属的子表，接着在相应的 SSTable 中执行查找操作。

#### 4) UpdateServer

UpdateServer 存储 OceanBase 系统的增量更新数据。UpdateServer 一般为一主一备，主备之间可以配置不同的同步模式。

集群内部同一时刻只允许一个 UpdateServer 提供写服务，RootServer 通过租约机制选择唯一的主 UpdateServer，当原先的主 UpdateServer 发生故障后，RootServer 能够在原先的租约失效后选择一台新的 UpdateServer 作为主 UpdateServer。

这种只有一台主 UpdateServer 提供写服务的方式，使得 OceanBase 很容易地实现跨行跨表事务，而不需要采用传 统的两阶段提交协议。

UpdateServer 是集群中唯一接受写入的模块，当执行更新操作时需要先写操作日志，并同步到备 UpdateServer，保证在主 UpdateServer 出现故障时，备 UpdateServer 中数据是一致的。

集群中的更新操作会写入内存表，当内存表的数据量超过一定值时，可以生成快照文件并转储到 SSD 中。

OceanBase 集群通过定期合并和数据分发这两种机制将 UpdateServer 一段时间之前的增量更新源源不断地分散到 ChunkServer，而 UpdateServer 只需要服务最新一小段时间新增的数据，这些数据是可以全部存放在内存中的。

#### 5) MergeServer

MergeServer 负责接收并解析用户的 SQL 请求，MergeServer 与客户端之间采用 MySQL 通信协议，MergeServer 从请求中提取 SQL 语句，经过词法分析、语法分析、查询优化等一系列操作后转发给相应的 ChunkServer 或者 UpdateServer。

MergeServer 缓存了 Tablet 的分布信息，根据请求涉及的 Tablet 将请求转发给该 Tablet 所在的 ChunkServer。如果是写操作，还会转发给 UpdateServer。

某些请求需要跨多个 Tablet，此时 MergeServer 会将请求拆分后发送给多台 ChunkServer，并合并这些 ChunkServer 返回的结果。如果请求涉及多个表格，则 MergeServer 需要先从 ChunkServer 获取每个表格的数据，再执行多表关联或者嵌套查询等操作。

以上系统架构中的所有组件使 OceanBase 既具有传统 DBMS 的跨行跨表事务、数据的强一致性及很短的查询修改响应时间，又具有云计算的海量数据管理能力、自动故障恢复、自动负载平衡及良好的扩展性。