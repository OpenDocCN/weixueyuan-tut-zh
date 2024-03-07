# HBase 是什么？

> 原文：[`c.biancheng.net/view/6499.html`](http://c.biancheng.net/view/6499.html)

HBase 是一个开源的、分布式的、版本化的非关系型数据库，它利用 Hadoop 分布式文件系统（Hadoop Distributed File System，HDFS）提供分布式数据存储。

HBase 是一个可以进行随机访问的存取和检索数据的存储平台，存储结构化和半结构化的数据，因此一般的网站可以将网页内容和日志信息都存在 HBase 里。

如果数据量不是非常庞大，HBase 甚至可以存储非结构化的数据。它不要求数据有预定义的模式，允许动态和灵活的数据模型，也不限制存储数据的类型。

HBase 是非关系型数据库，它不具备关系型数据库的一些特点，例如，它不支持 SQL 的跨行事务，也不要求数据之间有严格的关系，同时它允许在同一列的不同行中存储不同类型的数据。

HBase 作为 Hadoop 框架下的数据库，是被设计成在一个服务器集群上运行的。

## HBase 的发展历程

HBase 作为 Apache 基金会的 Hadoop 项目的一部分，使用 Java 语言实现，将 HDFS 作为底层文件存储系统，在此基础上运行 MapReduce 进行分布式的批量处理数据，为 Hadoop 提供海量数据管理的服务。

Apache HBase 最初是 Powerset 公司为了处理自然语言搜索产生的海量数据而开展的项目，由查德•沃特斯（Chad Walters）和吉姆•凯勒曼（Jim Kelleman）发起，经过两年发展成为 Apache 基金会的顶级项目。

HBase 是对 Google 的 Bigtable 的开源实现。

2006 年 11 月，Google 公司发表了论文 Bigtable: A Distributed Storage System for Structured Data，但是源码没有对外开放。

2007 年 2 月，项目发起人根据 Bigtable 的技术论文提出了作为 Hadoop 模块的 HBase 原型，该原型介绍了 HBase 的基本概念，以及数据库表、行键和底层数据存储结构的设计等。

由于 HBase 依赖 HDFS，它的版本发布都与 Hadoop 同步。

2007 年 10 月，第一个可用的 HBase 版本随同 Hadoop 0.15.0 版本发布，此版本只实现了最基本的模块和功能，因为处于初始开发阶段， HBase 功能还不够完善。

2008 年 1 月，Hadoop 升级为 Apache 的顶级项目，HBase 也作为 Hadoop 的子项目存在。其后 HBase 的发展非常活跃，两年间追随 Hadoop 的主版本发布了多个版本。

但在 2010 年 6 月发布 0.89.x 版本后不再与 Hadoop 发布关联，因为 Hadoop 的版本相对比较成熟，更新步伐减慢，而 HBase 处于活跃期，版本发布更加频繁。

同时，在 2010 年 HBase 成为 Apache 的顶级项目，此时的 HBase 已经基本实现了 Bigtable 论文中提出的功能。

2015 年 2 月发布了足够成熟的 HBase 1.0.0 版本。

从 Apache 官网上来看，十多年来 HBase 发布了很多版本，HBase 官方的最新版本信息如下所示。

```

HBase Releases
Please make sure you * re downloading from a nearby mirror site, not from www. apache. org. We suggest downloading the current stable release.
The 1.2.x series is the curre nt st able release line, it supercedes earlier release lines
Note that: 0.96 was EOM* d September 2014; 1.0 was EOM* d January 2016; 0.94 and 0.98 were EOM*d April 2017; 1.1 was EOM'd December 2017
For older versions, check the apache archive.
Name Last modified Size Description
Parent Directory   -
1.2.6/ 2017-10-04 18:53
1.3.2/ 2018-03-28 07:33 -
1.4.3/ 2018-04-03 05:09 -
2.0.0-beta-2/ 2018-03-08 01:40 . 一
hbase-thirdparty-1.0.17 2017-10-04 18:53 -
hbase-thirdparty-2.1.0/ 2018-03-23 23:38 -
stable/ 2017-10-04 18:53 -
```

从中可以看到，Apache 社区已经停止了 0.96、1.0、0.94、0.98、1.1 这些版本的使用，且目前已经发布了 1.3、1.4 版本，甚至 2.0 的测试版。但我们不建议使用非稳定的版本，目前稳定的版本是 1.2.6，读者可以从 stable 文件夹中获取，支持的 Hadoop 版本有 Hadoop 2.4.x、Hadoop 2.5.x、 Hadoop 2.6.1+ 和 Hadoop 2.7.1+。