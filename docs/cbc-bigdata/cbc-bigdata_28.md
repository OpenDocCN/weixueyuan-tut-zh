# Hadoop HBase 数据库简介

> 原文：[`c.biancheng.net/view/3584.html`](http://c.biancheng.net/view/3584.html)

HBase 是基于 Apache Hadoop 的面向列的 NoSQL 数据库，是 Google 的 BigTable 的开源实现。HBase 是一个针对半结构化数据的开源的、多版本的、可伸缩的、高可靠的、高性能的、分布式的和面向列的动态模式数据库。

HBase 和传统关系数据库不同，它采用了 BigTable 的数据模型增强的稀疏排序映射表（Key/Value
），其中，键由行关键字、列关键字和时间戳构成。

HBase 提供了对大规模数据的随机、实时读写访问。HBase 的目标是存储并处理大型的数据，也就是仅用普通的硬件配置，就能够处理上千亿的行和几百万的列所组成的超大型数据库。

Hadoop 是一个高容错、高延时的分布式文件系统和高并发的批处理系统，不适用于提供实时计算，而 HBase 是可以提供实时计算的分布式数据库，数据被保存在 HDFS (分布式文件系统）上，由 HDFS 保证其高容错性。

HBase 上的数据是以二进制流的形式存储在 HDFS 上的数据块中的，但是，HBase 上的存储数据对于 HDFS 是透明的。

HBase 可以直接使用本地文件系统，也可以使用 Hadoop 的 HDFS。HBase 中保存的数据可以使用 MapReduce 来处理，它将数据存储和并行计算有机地结合在一起。

HBase 是按列族进行数据存储的。每个列族会包括许多列，并且这些列是经常需要同时处理的属性。也就是说，HBase 把经常需要一起处理的列构成列族一起存放，从而避免了需要对这些列进行重构的操作。

HBase 在充分利用列式存储优势的同时，通过列族减少列连接的需求。