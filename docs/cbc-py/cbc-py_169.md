# Python SQLite 创建数据表（看了无师自通）

程序只要通过数据库连接对象打开游标，接下来就可以用游标来执行 DDL 语句，DDL 语句负责创建表、修改表或删除表。

使用 connect() 方法打开或创建一个数据库，例如如下代码：

conn = sqlite3.connect('first.db')

上面代码就用于打开或创建一个 SQLite 数据库。如果 first.db 文件（该文件就是一个数据库）存在，那么程序就是打开该数据库；如果该文件不存在，则会在当前目录下创建相应的文件（即对应于数据库）。

上面代码将会在当前目录下创建一个 first.db 文件，如果程序希望创建内存中的数据库，则只需将 first.db 改为特殊名称 :memory: 即可。

如下程序示范了如何创建数据表：

```
# 导入访问 SQLite 的模块
import sqlite3

# ①、打开或创建数据库
# 也可以使用特殊名：:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# ②、获取游标
c = conn.cursor()
# ③、执行 DDL 语句创建数据表
c.execute('''create table user_tb(
    _id integer primary key autoincrement,
    name text,
    pass text,
    gender text)''')
# 执行 DDL 语句创建数据表
c.execute('''create table order_tb(
    _id integer primary key autoincrement,
    item_name text,
    item_price real,
    item_number real,
    user_id inteter,
    foreign key(user_id) references user_tb(_id) )''')
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序使用 ①~⑤ 清晰地标出了使用 Python DB API 2.0 执行数据库访问的步骤。

上面程序中第 ③ 步执行了两次，每次分别执行一条 create 语句，因此该程序执行完成后，将会看到在当前数据库中包含两个数据表 user_tb 和 order_tb，且在 order_tb 表中有一个外键列引用 user_tb 表的 _id 主键列。

程序中第 ③ 步使用 execute() 方法执行的就是标准的 DDL 语句，因此只要读者拥有 SQL 语法知识，就可以使用 Python DB API 模块来执行这些 SQL 语句，非常简单。

SQLite 数据库所支持的 SQL 语句与 MySQL 大致相同，开发者完全可以把已有的 MySQL 经验“移植”到 SQLite 数据库上。当然，当 Python 程序提示某条 SQL 语句有语法错误时，最好先利用 SQLite 数据库管理工具（后续章节介绍）来测试这条语句，以保证这条 SQL 语句的语法正确。

需要指出的是，SQLite 内部只支持 NULL、INTEGER、REAL（浮点数）、TEXT（文本）和 BLOB（大二进制对象）这 5 种数据类型，但实际上 SQLite 完全可以接受 varchar(n)、char(n)、decimal(p, s) 等数据类型，只不过 SQLite 会在运算或保存时将它们转换为上面 5 种数据类型中相应的类型。

除此之外，SQLite 还有一个特点，就是它允许把各种类型的数据保存到任何类型的字段中，开发者可以不用关心声明该字段所使用的数据类型。

例如，可以把字符串类型的值存入 INTEGER 类型的字段中，也可以把数值类型的值存入布尔类型的字段中……但有一种情况例外，被定义为“INTEGER PRIMARY KEY”的字段只能存储 64 位整数，当使用这种字段保存除整数以外的其他类型的数据时，SQLite 会产生错误。

由于 SQLite 允许在存入数据时忽略底层数据列实际的数据类型，因此在编写建表语句时可以省略各数据列后面的类型声明。例如，对于 SQLite 数据库如下 SQL 语句也是正确的：

create table my_test
{
    _id_integer primary key autoincrement,
    name,
    pass,
    gender
};