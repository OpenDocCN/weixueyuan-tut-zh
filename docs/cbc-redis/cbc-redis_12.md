# HBase 的安装与配置（非常详细）

> 原文：[`c.biancheng.net/view/6515.html`](http://c.biancheng.net/view/6515.html)

本节讲述如何安装、部署 HBase 集群，以及如何通过命令行方式来完成 HBase 集群的启动和停止。

首先介绍部署 HBase 之前需要做的准备工作，如 Java、SSH 和 Hadoop 这些先决条件的配置；然后介绍如何安装 HBase，以及如何配置集群中相关文件。

同时需要注意的是，本节介绍的是分布式 HBase 集群的部署，在对一台机器修改配置文件后需要同步到集群中的所有节点。

## 准备工作

Hadoop 和 HBase 可以工作在 Linux 和 Windows 操作系统上，但是在 Windows 上安装 Hadoop 和 HBase 只适用于评估和测试，一个成熟稳定的 Hadoop+HBase 集群还是需要运行在 Linux 上， 因此本节中的安装配置及操作命令都是在 Linux 中完成的。Linux 操作系统的选择也有多个，本节使用的是 [CentOS 7](https://www.centos.org/)，可以从其官网上获取。

除了操作系统的选择外，还涉及分布式 Hadoop 的部署、JDK、SSH 等，在运行 HBase 之前要确保这些条件已经具备。JDK 和 SSH 的配置同时也是 Hadoop 安装的前提条件，因此，如何配置 JDK 和 SSH 本节也不做介绍，只介绍这些软件在运行 HBase 时的作用。

####  JDK

JDK 是 Hadoop 和 HBase 运行的环境。Hadoop 和 HBase 都采用 Java 语言实现，它们的守护进程都运行在 JVM 下，所以安装 JDK 是基本要求，而且使用 JDK 1.6 以上的版本才能更好地支持 HBase，同时为了能够管理超过 4GB 的内存空间，需要安装 64 位的 JDK。

JDK 版本一般选择 Oracle 公司推荐的版本，用户最好使用一个稍旧（稳定）的版本，因为使用最新 Java 版本时可能会出现意想不到的问题，本实验使用的是 jdk-8u161-linux-x64.rpm 包。

#### SSH

配置 SSH 可以实现简单的服务器到主机的通信，在集群中，只有启动 sshd 后，才可以通过脚本远程操控其他的 Hadoop 和 HBase 进程。但是进程间通信时每次都需要输入密码，因此，为了实现自动化操作，需要配置 SSH 免密码的登录方式。

本节中使用的 CentOS 操作系统内置了 SSH 服务，只需做简单配置即可。

#### Hadoop

HBase 是以 HDFS 作为底层存储文件系统的，因此 Hadoop 是先行条件。Hadoop 安装文件可从 [Hadoop 官网](http://hadoop.apache.org/)上获取，本节使用的是 Hadoop 2.7.6 版本。

## HBase 的安装与配置

安装 Java、SSH 和 Hadoop 以后，接下来要安装配置 HBase。HBase 的运行模式包括单机、伪分布式和分布式三种。

单机模式使用本地文件系统，所有进程运行在一个 JVM 上，单机模式一 般只用于测试，HBase 需要结合 Hadoop 才能展现出其分布式存储的能力。

伪分布式和分布式模式是一种主从模式，基本由一个 Master 节点和多个 Slave 节点组成，均使用 HDFS 作为底层文件系统。但伪分布式模式下，所有进程运行在一个 JVM 上，可以进行小集群的配置，用于测试。在生产环境下，需要在不同机器上的 JVM 中运行守护进程。

下面着重介绍伪分布式和分布式两种模式的安装与配置。

#### 下载安装

本节使用 HBase 1.2.6 版本，安装文件可以从 [HBase 官网](https://hbase.apache.org/)下载。下载完成后，解压 TAR 包到指定的目录，如/usr/local 目录，并切换到该目录下，查看已解压的文件，然后用 mv 命令重命名 hbase-1.2.6 文件夹为 hbase。

[root@localhost ~]# tar xzvf hbase-1.2.6-bin.tar.gz /usr/local
[root@localhost ~]# cd /usr/local/    //切换到 hbase 所在目录
[root@localhost local] # mv hbase-1.2.6 hbase    //重命名为 hbase
[root@localhost local] # Is    //查看此目录下文件

bin etc games hadoop hbase hbase-1.2.6-bin.tar.gz include lib

#### 修改配置文件

在配置伪分布式和分布式集群时，需要修改安装目录下 conf 文件夹中相关的配置文件，主要涉及以下两个文件，同时需要将这些配置文件分发到集群中的各个 Regionserver 节点。

*   hbase-env.sh：配置 HBase 运行时的变量，如 Java 路径、RegionServer 相关参数等。
*   hbase-site.xml:在这个文件中可以添加 HBase 的相关配置，如分布式的模式、ZooKeeper 的配置等。

首先修改 hbase-env.sh 文件，配置 Java 的运行环境，将其中的 JAVA_HOME 指向 Java 的安装目录，编辑 hbase-env.sh 文件，添加下面这一行代码：

export JAVA_HOME=/usr/j ava/j dkl.8.0_161

另外，ZooKeeper 可以作为 HBase 的一部分来管理启动，即 ZooKeeper 随着 HBase 的启动而启动，随其关闭而关闭，这时需要在 hbase-env.sh 中设置 HBASE_MANAGES_ZK 变量，即添加下面这一行代码：

export HBASE_MANAGES_ZKrue

当然 ZooKeeper 也可以作为独立的集群来运行，即完全与 HBase 脱离关系，这时需要设置 HBASE_MANAGES_ZK 变量为 false。

下面通过一个分布式集群示例来介绍如何配置 hbase-site.xml 文件，如下示例中的 <description></description> 标记对之间的内容是对每个属性的中文解释，在具体使用时可以不用修改，使用默认设置即可，代码如下：

```

<configuration>
    <property>
        <name> hbase.rootdir </name>
        <value>hdfs://example0:9000/hbase</value>
        <description> hbase.rootdir 是 RegionServer 的共享目录，用于持久化存储 HBase 数据，默认写入/tmp 中。如果不修改此配置，在 HBase 重启时，数据会丢失。此处一般设置的是 hdfs 的文件目录，如 NameNode 运行在 namenode.Example.org 主机的 9090 端口，则需要设置为 hdfs://namenode.example.org:9000/hbase
        </description>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
        <description>此项用于配置 HBase 的部署模式，false 表示单机或者伪分布式模式，true 表不完全分布式模式。
        </description>
    </property>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>examplelz example2,example3</value>
        <description>此项用于配置 ZooKeeper 集群所在的主机地址。examplel、 example2、example3 是运行数据节点的主机地址。   
        </description>
    </property>
    <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/var/zookeeper</value>
        <description>此项用于设置存储 ZooKeeper 的元数据，如果不设置默认存在/tmp 下，重启时数据会丢失。
        </description>
    </property>
</configuration>
```

当然还有很多其他参数可以根据需求进行设置，具体可查看官网上的配置项。

另外，在完全分布式模式下，还需要修改 conf/regionservers 文件，此文件罗列了所有 Region 服务器的主机名。HBase 的运维脚本会依次迭代访问每一行来启动所有的 Region 服务器进程。配置完上述文件后，需要同步这些文件到集群上的其他节点。

#### HBase 的启动和关闭

在 Master 服务器上已经配置了对集群中所有 Slave 机器的无密码登录，使用 start-hbase.sh 脚本即可启动整个集群。

首先，确认 HDFS 处于运行状态，使用 jps 命令查看 NameNode 和 DataNode 的服务是否正常启动。以下示例为伪分布式模式，因此 NameNode 和 DataNode 都在同一台机器上运行。

[root@localhost local]# jps
24371 NameNode
24680 SecondaryNameNode
24506 DataNode

然后，用如下命令启动 HBase，下例启动的是伪分布式集群，使用完全分布式模式的启动命令也是如此。

[rootglocalhost hbase]# bin/start-hbase.sh
localhost: starting zookeeper, logging to /usr/local/hbase/bin/../logs/hbase-root-zookeeper-localhost.localdomain.out
starting mas ter, logging to /usr/local/hbase/bin/../logs/hbase-root-master-localhost. localdomain.out
starting regionserver, logging to /usr/local/hbase/bin/../logs/hbase-root-1-regionserver-localhost.localdomain.out

接着，使用 jps 命令查看进程，如果是完全分布式模式，则在 Master 节点运行有 Hmaster 和 HQuorumPeer 进程，在 Slave 节点上运行 HRegionServer 和 HQuorumPeer 进程。

[rootQlocalhost local]# jps
24371 NameNode
24680 SecondaryNameNode
26328 HRegionServer
24506 DataNode
26508 Jps
25455 HQuorumPeer
25519 HMaster

以上显示 HBase 集群正常启动，可以输入 hbase shell 命令进入 HBase 执行数据库的操作。

最后，使用 stop-hbase.sh 脚本关闭 HBase 集群，如下所示：

[root@localhost hbase]# bin/stop-hbase.sh
stopping hbase................................
localhost: stopping zookeeper.