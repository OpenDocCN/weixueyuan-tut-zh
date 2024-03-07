# Python（SQLite）fetchone、fetchmany 和 fetchall 用法

执行查询依然按照前面介绍的步骤进行，只是改为执行 select 语句。由于 select 语句执行完成后可以得到查询结果，因此程序可通过游标的 fetchone()、fetchmany(n)、fetchall() 来获取查询结果。正如它们的名字所暗示的，fetchone() 用于获取一条记录，fetchmany(n) 用于获取 n 条记录，fetchall() 用于获取全部记录。

如下程序示范了如何执行查询语句，并输出查询结果。

```
# 导入访问 SQLite 的模块
import sqlite3

# ①、打开或创建数据库
# 也可以使用特殊名:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# ②、获取游标
c = conn.cursor()
# ③、调用执行 select 语句查询数据
c.execute('select * from user_tb where _id > ?', (2,))
print('查询返回的记录数:', c.rowcount)
# 通过游标的 description 属性获取列信息
for col in (c.description):
    print(col[0], end='\t')
print('\n--------------------------------')
while True:
    # 获取一行记录，每行数据都是一个元组
    row = c.fetchone()
    # 如果抓取的 row 为 None，退出循环
    if not row :
        break
    print(row)
    print(row[1] + '-->' + row[2])
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序使用 execute() 方法执行了一条 select 语句，接下来程序使用循环，通过游标的 description 属性获取查询的列信息，也可以通过游标来获取查询结果，如上面程序所示。

运行上面程序，可以看到如下运行结果：

_id name pass gender
--------------------------------
(3, 'bai', '123456', 'female')
bai-->123456
(4, 'zhu', '123456', 'male')
zhu-->123456
(5, 'niu', '123456', 'male')
niu-->123456
(6, 'tang', '123456', 'male')
tang-->123456

从上面的运行结果来看，程序返回了所有 _id 大于 2 的记录，这就是上面程序查询所返回的结果。

由于每条 Select 语句都可能返回多个查询结果，因此不能使用 executemany() 执行查询语句，远没什么意义。

注意，不要试图使用 executemany()方法执行 select 语句，否则程序将会报错：ProgrammingError:executemany() can only execute DML statements。

上面程序使用 fetchone() 方法每次获取一条记录，这是比较常见的做法。实际上，程序也可以使用 fetchmany(n) 或 fetchall() 方法一次获取多条记录。

例如，可将上面程序中第 12~23 行代码改为如下形式：

```
# 通过游标的 description 属性获取列信息
for col in (c.description):
    print(col[0], end='\t')
print('\n--------------------------------')
while True:
    # 每次抓取 3 条记录，该方法返回一个由 3 条记录组成的列表
    rows = c.fetchmany(3)
    # 如果抓取的 rows 为 None，退出循环
    if not rows :
        break
    # 再次使用循环遍历获取的列表
    for r in rows:
        print(r)
```

上面程序使用 fetchmany(3) 每次获取 3 条记录，该方法返回由 3 条记录组成的列表，因此程序还需要遍历该列表才能取出每条记录。

一般来说，在程序中应该尽量避免使用 fetchall() 来获取查询返回的全部记录。这是因为程序可能并不清楚实际查询会返回多少条记录，如果查询返回的记录数量太多，那么调用 fetchall() 一次获取全部记录可能会导致内存开销过大，情况严重时可能导致系统崩溃。