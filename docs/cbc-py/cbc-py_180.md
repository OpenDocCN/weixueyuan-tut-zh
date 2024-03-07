# Python MySQL 数据库执行 DML 语句

与使用 SQLite 数据库模块类似，MySQL 数据库模块同样可以使用游标的 execute() 方法执行 DML 的 insert、update、delete 语句，对数据库进行插入、修改和删除数据操作。

例如，如下程序示范了向数据库的两个数据表中分别插入一条数据：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用执行 insert 语句插入数据
c.execute('insert into user_tb values(null, %s, %s, %s)',
    ('孙悟空', '123456', 'male'))
c.execute('insert into order_tb values(null, %s, %s, %s, %s)',
    ('鼠标', '34.2', '3', 1))
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序中第 11、13 行代码分别用于向 user_tb 和 order_tb 表中插入数据。注意该程序的 SQL 语句中的占位符：%s，这正如 mysql.connector.paramstyle 属性所标识的 pyformat，它指定在 SQL 语句中使用扩展的格式代码来作为占位符。程序运行完成之后，就会向 python 数据库的两个数据表中各插入一条记录。

与 SQLite 数据库模块类似的是，MySQL 数据库模块同样支持使用 executemany() 方法重复执行一条 SQL 语句。例如如下程序：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用 executemany()方法把同一条 SQL 语句执行多次
c.executemany('insert into user_tb values(null, %s, %s, %s)',
    (('sun', '123456', 'male'),
    ('bai', '123456', 'female'),
    ('zhu', '123456', 'male'),
    ('niu', '123456', 'male'),
    ('tang', '123456', 'male')))
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

该程序与前面使用 SQLite 数据库模块重复执行 SQL 语句的程序基本相同，只是该程序在 SQL 语句中使用了 %s 作为占位符。

使用 MySQL 数据库模块中游标的 executemany() 方法同样可重复执行 update、delete 语句：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用 executemany()方法把同一条 SQL 语句执行多次
c.executemany('update user_tb set name=%s where user_id=%s',
    (('小孙', 2),
    ('小白', 3),
    ('小猪', 4),
    ('小牛', 5),
    ('小唐', 6)))
# 通过 rowcount 获取被修改的记录条数
print('修改的记录条数：', c.rowcount)
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

需要说明的是，MySQL 数据库模块的连接对象有一个 autoconunit 属性，如果将该属性设为 True，则意味着关闭该连接的事务支持，程序每次执行 DML 语句之后都会自动提交，这样程序就无须调用连接对象的 commit() 方法来提交事务了。例如如下程序：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# 将 autocommit 设置 True，关闭事务
conn.autocommit = True
#下面执行的 DML 语句会自动提交
...
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

在上面程序中，将连接对象的 autocommit 设为 True，这意味着该连接将会自动提交每条 DML 语句，相当于关闭了事务，所以程序也不需要调用连接对象的 commit() 方法来提交事务。