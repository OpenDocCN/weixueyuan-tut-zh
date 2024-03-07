# Python pip（管理模块工具）基础用法

前面章节在介绍安装 MySQL 服务器时，已经选择安装 MySQL 提供的 Python 数据库模块，而且根据最后的安装提示，显示己经安装成功。

现在的问题是，我们有没有办法来查看是否己经安装成功了呢？或者说，如果在安装 MySQL 服务器时忘记了选择 Connector/Python 模块，现在是否还有补救措施？

答案是肯定的。Python 自带了一个 pip 工具用来查看、管理所安装的各种模块。

## 查看已安装的模块

查看已安装的模块，使用如下命令：

pip show packagename

启动命令行窗口，在窗口中输入如下命令：

pip show mysql-connector-python

在上面的命令中，mysql-connector-python 就是该模块的名字。运行该命令，可以看到如下输出结果：

Name: mysql-connector-python
Version: 8.0.11
Summary: MySQL driver written in Python
Home-page: http://dev.mysql.com/doc/connector-python/en/index.html
Author: Oracle and/or its affiliates
Author-email: UNKNOWN
License: GNU GPLv2 (with FOSS License Exception)
Location: d:\python\python36\lib\site-packages
Requires: protobuf
Required-by:

从上面的输出结果可以看到，已经成功安装了 mysql-connector-python 8.0.11，以及该模块的官方网址和安装路径等有用的信息。

## 卸载己安装的模块

卸载己安装的模块，使用如下命令：

pip uninstall packagename

在命令行窗口中输入如下命令：

pip uninstall mysql-connector-python

运行该命令，可以看到如下输出结果：

Uninstalling mysql-connector-python 8.0.11:
  Would remove:
    d:\python\python36\lib\site-packages\mysql
    d:\python\python36\lib\site-packages\mysql_connector_python-8.0.11-py3.6.egg-info
    d:\python\python36\lib\site-packages\mysqlx
Proceed (y/n)?

上面的提示信息询问是否要删除 mysql-connector-python 模块，如果删除该模块，将会删除 3 个目录。如果希望删除，则可以在输入“y”之后按回车键。接下来将看到系统提示如下信息：

Successfully uninstalled mysql connector-python-8.0.11

该信息显示 mysql-connector-python-8.0.11 被删除成功。

执行该删除命令后，Python 将不再包含 mysql-connector-python 模块，相当于在安装 MySQL 服务器时没有选择 Connector/Python 模块。

如果要查看已安装的所有模块，可以使用如下命令：

pip list

## 安装模块

安装模块，使用如下命令：

pip install packagename

在命令行窗口中输入如下命令：

pip install mysql-connectar-python

运行该命令，将看到程序下载并安装 mysql-connector-python 模块的过程，最后会生成如下提示信息：

Successfully installed mysql-connector-python-8.0.11

上面的信息提示该模块安装成功。

如果希望安装不同版本的模块，则可指定版本号。例如：

pip install packagename ==1.0.4 #安装指定版本

除使用 MySQL 官方提供的 Python 模块来连接 MySQL 数据库之外，还有一个使用广泛的连接 MySQL 数据库的模块 MySQL-python，其官方站点为 [`pypi.org/project/MySQL-python/`](https://pypi.org/project/MySQL-python/)。