# C# ADO.NET 数据库操作及常用类概述

> 原文：[`c.biancheng.net/view/3004.html`](http://c.biancheng.net/view/3004.html)

在 C# 语言中 ADO.NET 是在 ADO 的基础上发展起来的，ADO (Active Data Object) 是一个 COM 组件类库，用于访问数据库，而 ADO.NET 是在 .NET 平台上访问数据库的组件。

ADO.NET 是以 ODBC (Open Database Connectivity) 技术的方式来访问数据库的一种技术。

ADO.NET 中的常用命名空间如下表所示。

| 命名空间 | 数据提供程序 |
| System.Data.SqlClient | Microsoft SQL Server |
| System.Data.Odbc  | ODBC |
| System.Data.OracleClient | Oracle |
| System.Data.OleDb | OLE DB |

在使用 ADO.NET 进行数据库操作时通常会用到 5 个类，分别是 Connection 类、 Command 类、DataReader 类、DataAdapter 类、DataSet 类。

在接下来的讲解中我们将以连接 SQL Server 为例介绍 ADO.NET 中的对象，引用的命名空间为 System.Data.SqlClient。

除了 DataSet 类以外，其他对象的前面都加上 Sql，即 SqlConnection、SqlCommand、SqlDataReader、SqlDataAdapter。

#### 1) Connection 类

该类主要用于数据库中建立连接和断开连接的操作，并且能通过该类获取当前数据库连接的状态。

使用 Connection 类根据数据库的连接串能连接任意数据库，例如 SQLServer、Oracle、MySQL 等。

但是在 .NET 平台下，由于提供了一个 SQL Server 数据库，并额外提供了一些操作菜单便于操作，所以推荐使用 SQLServer 数据库。

#### 2) Command 类

该类主要对数据库执行增加、删除、修改以及查询的操作。

通过在 Command 类的对象中传入不同的 SQL 语句，并调用相应的方法来执行 SQL 语句。

#### 3) DataReader 类

该类用于读取从数据库中查询出来的数据，但在读取数据时仅能向前读不能向后读， 并且不能修改该类对象中的值。

在与数据库的连接中断时，该类对象中的值也随之被清除。

#### 4) DataAdapter 类

该类与 DataSet 联用，它主要用于将数据库的结果运送到 DataSet 中保存。

DataAdapter 可以看作是数据库与 DataSet 的一个桥梁，不仅可以将数据库中的操作结果运送到 DataSet 中，也能将更改后的 DataSet 保存到数据库中。

#### 5) DataSet 类

该类与 DataReader 类似，都用于存放对数据库查询的结果。

不同的是，DataSet 类中的值不仅可以重复多次读取，还可以通过更改 DataSet 中的值更改数据库中的值。

此外，DataSet 类中的值在数据库断开连接的情况下依然可以保留原来的值。