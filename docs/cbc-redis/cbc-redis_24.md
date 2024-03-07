# HBase Thrift 协议编程入门教程

> 原文：[`c.biancheng.net/view/6525.html`](http://c.biancheng.net/view/6525.html)

Thrift 是一个软件框架，用来进行可扩展且跨语言的服务的开发。Thrift 定义了一种描述对象和服务的接口定义语言（Interface Definition Language, IDL），它先通过 IDL 来定义 RPC 的接口和数据类型，再通过编译器生成不同语言的代码，并由生成的代码负责 RPC 协议层和传输层的实现。

Thrift 支持的语言有 C++、Java、Python、PHP 等。下面将使用 Python 语言来实现通过 Thrift 客户端访问 HBase，这种方式完全脱离了 Java 和 JVM。

Thrift 实际上是实现了 C/S 模式，通过代码生成工具将接口定义文件生成服务器端和客户端代码，从而实现服务端和客户端跨语言的支持，例如，客户端用 Python 实现，服务器使用 Java 实现。用户在 Thirft 描述文件中声明自己的服务，这些服务经过编译后会生成相应语言的代码文件，然后用户实现服务即可。

Thrift 包含了三个重要的组件，分别为 protocal、transpor t 和 server。 其中，protocol 是协议层，用来定义数据的传输格式；transport 是传输层，定义数据的传输方式，可以为 TCP/IP 传输也可以是内存共享的方式；server 定义支持的服务模型。

下面来看如何使用 Thrift 生成 Python 语言的客户端访问 HBase。

## HBase 客户端环境部署

使用 Python 调用 HBase，需要启动 Thrift 服务，因此，需要先安装 Thrift。由于 Linux 操作系统中没有内置 Thrift 的安装包，因此需要手工建立 Thrift。

从 Thrift 官网上下载源码 tar 包，并解压到常用目录下：

[root@localhost local]# wget http://www.apache.org/dyn/closer.cgi?path=/thrift/O.11.0/thrift-0.11.0.tar.gz
[root@localhost local]#tar -xzvf thrift-0..0.tar.gz

在编译安装 Thrift 之前，需要安装依赖的库，如 Automake、LibTool、Bison、Boost 等，具体依赖库的安装参考 Thrift 官网安装手册，依赖库下载完后，即可编译并安装 Thrift：

[root@localhost thrift-0.11.0]# ./configure
[root@localhost thrift-0.11.0]# make
[root@localhost thrift-0.11.0]# make install

通过调用 thrift 命令可以验证安装是否成功：

[root@localhost ~]# thrift -version
Thrift version 0.11.0

HBase 的源代码中，hbase.thrift 文件描述了 HBase 服务 API 和有关对象的 IDL 文件，需要使用 thrift 命令对此文件进行编译，生成 Python 连接 HBase 的库包。

Hbase.thrift 文件在 hbase-1.2.6/hbase- thrift/src/main/resources/org/apache/hadoop/hbase/thrift 目录下，编译操作如下:

//创建目录
[root@localhost local] # mkdir pythonhbase
//切换到该目录下
[root@localhost local]# cd pythonhbase
//执行 thrift 生成 HBase 的 Python 库
[root@localhost pythonhbase]#thrift --gen py . . /hbase-1.2.6/hbase-thrift/src/main/ resources/org/apache/hadoop/hbase/thrift/Hbase.thrift

生成的库文件在 gen-py 的子目录下，如下所示：

[root@localhost gen-py]# find .
./hbase
./hbase/init_.py
./hbase/ttypes.py
./hbase/constants.py
./hbase/Hbase.py
./hbase/Hbase-remote
./hbase/_init_.pyc
./hbase/Hbase.pyc
./hbase/ttypes.pyc
./_init_.py

将 gen-py 目录下的 hbase 子目录复制到工程目录 python3.6/site-packages/hbase 下直接使用，如下所示：

[root@localhost pythonhbase.py]# python
Python 2.7.5 (default, Apr 11 2018, 07:36:10)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import thrift
>>>imp or t hbase

这些命令执行成功后，表示使用 Python 语言访问 HBase 的客户端已经完成。

## HBase Thrift 服务启动

在服务器上使用 star-hbase.sh 命令启动 HBase 服务，使用 jps 命令确定 HBase 已经启动并正在运行后，启动 Thrift 服务，使用默认的设置即可正常工作，如下所示：

[root@localhost bin]# jps
66051 HQuorumPeer
66391 Jps
65577 SecondaryNameNode
65401 DataNode
66152 HMaster
66280 HRegionServer
65262 NameNode
[rootQlocalhos t bin：] # ./hbase t hrift start
...
2018-07-05 17:05:10,431 INFO [main] thrift.ThriftServerRunner: starting TBoundedThreadPoolServer on /0.0.0.0:9090 with readTimeout 60000ms; min worker threads=16z max worker threads=1000z max queued requests=1000

目前客户端与服务器都已准备妥善，接下来测试其是否可用。在客户端的 Python 项目目录下 打开一个终端窗口，再次启动 Python：

[root@localhost pythonhbase]# python
Python 2.7.5 (defaultA Apr 11 2018, 07:36:10)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>from thrift.transport import TSocket
>>>from thrift.protocol import TBinaryProtocol
>>>from thrift import Thrift
>>>from hbase import Hbase
>>>transport = TSocket.TSocket('localhost', 9090)
>>>protocol = TBinaryProtocol.TBinaryProtocol(transport)
>>>client = Hbase.Client (protocol)
>>>transport. open ()
>>>result = client.getTableNames()
>>>print result
[ 'base_thrift', 't1', 't2', 'test' ]
>>>transport.close ()

以上代码简单地完成了使用 Thrift 客户端访问 HBase,另外还有创建表、插入数据、查询数据等操作。用户可查询 Python 项目下的 hbase/hbase.py 文件，里面提供了各种操作方法。