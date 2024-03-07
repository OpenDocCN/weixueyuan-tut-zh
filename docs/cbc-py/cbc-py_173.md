# Python（SQLite）executescript 用法（附带实例讲解）

SQLite 数据库模块的游标对象还包含了一个 executescript() 方法，这不是一个标准的 API 方法，这意味着在其他数据库 API 模块中可能没有这个方法。但是这个方法却很实用，它可以执行一段 SQL 脚本。

例如，如下程序使用 executescript() 方法执行一段 SQL 脚本：

```
# 导入访问 SQLite 的模块
import sqlite3

# ①、打开或创建数据库
# 也可以使用特殊名：:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# ②、获取游标
c = conn.cursor()
# ③、调用 executescript()方法执行一段 SQL 脚本
c.executescript('''
    insert into user_tb values(null, '武松', '3444', 'male'); 
    insert into user_tb values(null, '林冲', '44444', 'male');
    create table item_tb(_id integer primary key autoincrement, name, price);
    ''')
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序调用 executescript() 方法执行一段复杂的 SQL 脚本，在这段 SQL 脚本中包含了两条 insert 语句，该语句负责向 user_tb 表中插入记录，还使用 create 语句创建了一个数据表。

运行上面程序，可以看到 first.db 数据库中多了一个 item_tb 数据表，user_tb 数据表被插入了两条记录。

此外，为了简化编程，SQLite 数据库模块还为数据库连接对象提供了如下 3 个方法：

1.  execute(sql[, parameters])：执行一条 SQL 语句。
2.  executemany(sql[, parameters])：根据序列重复执行 SQL 语句。
3.  executescript(sql_script)：执行 SQL 脚本。

读者可能会发现，这 3 个方法与游标对象所包含的 3 个方法完全相同。事实正是如此，数据库连接对象的这 3 个方法都不是 DB API 2.0 的标准方法，它们只是游标对象的 3 个方法的快捷方式，因此在用法上与游标对象的 3 个方法完全相同。