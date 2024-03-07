# Python MySQL 数据库执行查询语句

使用 MySQL 数据库模块执行查询语句，与使用 SQLite 数据库模块执行查询语句基本相似，只需注意 SQL 语句中的占位符的差别即可。

例如，如下程序示范了查询 MySQL 数据库中的数据：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用执行 select 语句查询数据
c.execute('select * from user_tb where user_id > %s', (2,))
# 通过游标的 description 属性获取列信息
for col in (c.description):
    print(col[0], end='\t')
print('\n--------------------------------')
# 直接使用 for 循环来遍历游标中的结果集
for row in c:
    print(row)
    print(row[1] + '-->' + row[2])
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序中，第 11 行代码调用 execute() 方法执行 select 语句查询数据，在该 SQL 语句中同样使用了 %s 作为占位符，这就是与 SQLite 数据库模块的差别。

该程序直接使用 for 循环来遍历游标所包含的查询数据，这完全是可以的，因为游标本身就是可遍历对象。运行上面的程序，可以看到如下查询结果：

user_id name pass gender
--------------------------------
(3, '小白'，'123456'，'female')
小白-->123456
(4, '小猪', '123456', 'male')
小猪-->123456
(5, '小牛', '123456', 'male')
小牛-->123456
(6, '小唐', '123456', 'male')
小唐-->123456
(7, '孙悟空', '123456', 'male')
孙悟空-->123456

MySQL 数据库模块的游标对象同样支持 fetchone()、fetchmany()、fetchall() 方法，例如如下程序使用 fetchmany() 方法每次获取 3 条记录：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用执行 select 语句查询数据
c.execute('select * from user_tb where user_id > %s', (2,))
# 通过游标的 description 属性获取列信息
for col in (c.description):
    print(col[0], end='\t')
print('\n--------------------------------')
while True:
    # 每次抓取 3 条记录，该方法返回一个多个元组组成的列表
    rows = c.fetchmany(3)
    # 如果抓取的 row 为 None，退出循环
    if not rows :
        break
    # 再次使用循环遍历获取的列表
    for r in rows:
        print(r)
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```