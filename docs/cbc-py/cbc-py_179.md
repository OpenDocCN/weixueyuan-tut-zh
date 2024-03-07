# Python MySQL 数据库执行 DDL 语句

在使用 mysql-connector-python 模块操作 MySQL 数据库之前，同样先检查一下该模块的全局属性。

>>> import mysql.connectar
>>> mysql.connector.apilevel
'2.0'
>>> mysql.connector.paramstyle
'pyformat'
>>>

从上面的输出信息可以看到，mysql-connector-python 数据库模块同样遵守 DB API 2.0 规范，且该模块允许在 SQL 语句中使用扩展的格式代码（pyformat）来代表参数。

使用 MySQL 模块对 MySQL 数据库执行 DDL 语句，与使用 SQLite 模块对 SQLite 数据库执行 DDL 语句并没有太大的区别，只是 MySQL 数据库有服务器进程，默认通过 3306 端口对外提供服务。因此，Python 程序在连接 MySQL 数据库时可指定远程服务器 IP 地址和端口，如果不指定服务器 IP 地址和端口，则使用默认的服务器 IP 地址 localhost 和默认端口 3306。

下面程序示范了如何连接 MySQL 数据库，并通过 DDL 语句来创建两个数据表：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、执行 DDL 语句创建数据表
c.execute('''create table user_tb(
    user_id int primary key auto_increment,
    name varchar(255),
    pass varchar(255),
    gender varchar(255))''')
# 执行 DDL 语句创建数据表
c.execute('''create table order_tb(
    order_id integer primary key auto_increment,
    item_name varchar(255),
    item_price double,
    item_number double,
    user_id int,
    foreign key(user_id) references user_tb(user_id) )''')
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

与连接 SQLite 的程序相比，上面程序最大的区别就在于第 5 行代码，程序要连接 localhost 主机上 3306 端口服务的 python 数据库，必须先在本机的 MySQL 数据库服务器中创建一个 python 数据库。

通过“开始”菜单中的“MySQL→MySQL Server 8.0->MySQL 8.0 Command Line Client-Unicode”启动 MySQL 的命令行客户端，输入在 root 账户设置的密码，即可进入 MySQL 的命令行客户端，然后输入如下命令来创建 python 数据库：

create database python;

运行上面的程序，当程序运行结束后，将可以看到 python 数据库中多了两个数据表。

上面程序中的 ①~⑤ 步与前面并无区别，程序在第 ③ 步执行了两次，每次分别执行一条 create 语句。因此，该程序执行完成后，将会看到当前数据库中包含两个数据表，user_tb 和 order_tb，且在 order_tb 表中有一个外键列引用了 user_tb 表的 user_id 主键列。

需要指出的是，此处程序使用 execute() 方法执行的 create 语句与前面操作 SQLite 数据库时所使用的 create 语句略有差异，但这个差异是由两个数据库本身所引起的，与 Python 程序并没有任何关系。

如果 Python 程序提示某条 SQL 语句有语法错误，则最好先利用此处介绍的 MySQL 的命令行客户端测试这条 SQL 语句，以保证该语句的语法正确。

同一条 SQL 语句，在有的数据库上可能是成功的，而在其他数据库上可能会失败，这是由于不同数据库所支持的 SQL 虽然大体是相同的，但在实现细节上略有差异。