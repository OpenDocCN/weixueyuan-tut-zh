# HDFS 的特点与使用场景

> 原文：[`c.biancheng.net/view/6508.html`](http://c.biancheng.net/view/6508.html)

上节《HDFS 入门教程》讲解了 HDFS 的底层原理，本节接着讲解一下 HDFS 的特点以及使用场景有。

## 适合存储超大文件

HDFS 支持 GB 级别甚至 TB 级别的文件，每个文件大小可以大于集群中任意一个节点的磁盘容量，文件的所有数据块存在不同节点中，在进行大文件读写时采用并行的方式提高数据的吞吐量。

HDFS 不适合存储大量的小文件，这里的小文件指小于块大小的文件。因为 NameNode 将文件系统的元数据信息存在内存当中，所以 HDFS 所能存储的文件总数受到 NameNode 内存容量的限制。

下面通过举例来计算同等容量的单个大文件和多个小文件所占的文件块的个数。

假设 HDFS 中块的大小为 64MB，备份数量为 3，—般情况下，一条元数据记录占用 200B 的内存。那么，对于 1GB 的大文件，将占用 1GB/64MB x 3 个文件块；对于 1024 个 1MB 的小文件，则占用 1024 x3 个文件块。可以看到，存储同等大小的文件，单个文件越小，所需的元数据信息越大，占用的内存越大，因此 HDFS 适合存储超大文件。

## 适用于流式的数据访问

HDFS 适用于批量数据的处理，不适用于交互式处理。它设计的目标是通过流式的数据访问保证高吞吐量，不适合对低延迟用户响应的应用。可以选择 HBase 满足低延迟用户的访问需求。

## 支持简单的一致性模型

HDFS 中的文件支持一次写入、多次读取，写入操作是以追加的方式添加在文件末尾，不支持多个写入者的操作，也不支持对文件的任意位置进行修改。

## 计算向数据靠拢

在 Hadoop 系统中，对数据进行计算时，采用将计算向数据靠拢的方式，即选择最近的数据进行计算，减少数据在网络中的传输延迟。