# Python SQLite create_aggregate 方法：自定义聚集函数

标准的 SQL 语句提供了如下 5 个标准的聚集函数：

*   sum()：统计总和。
*   avg()：统计平均值。
*   count()：统计记录条数。
*   max()：统计最大值。
*   min()：统计最小值。

如果程序需要在 SQL 语句中使用与其他业务相关的聚集函数，则可使用数据库连接对象所提供的`create_aggregate(name, num_params, aggregate_class)` 方法，该方法用于注册一个自定义的聚集函数。

create_aggregate 方法包含 3 个参数：

*   name：指定自定义聚集函数的名字。
*   num_params：指定聚集函数所需的参数。
*   aggregate_class：指定聚集函数的实现类。该类必须实现 step(self, params...) 和 finalize(self) 方法，其中 step() 方法对于查询所返回的每条记录各执行一次；finalize(self) 方法只在最后执行一次，该方法的返回值将作为聚集函数最后的返回值。

假设需要查询 user_tb 表中长度最短的密码，此时就需要用到自定义的聚集函数。下面程序使用 create_aggregate() 方法为 SQL 语句注册一个自定义的聚集函数，然后程序就可以在 SQL 语句中使用该自定义的聚集函数：

```
# 导入访问 SQLite 的模块
import sqlite3

# 先定义一个普通类，准备注册为 SQL 中的自定义聚集函数
class MinLen:
    def __init__(self):
        self.min_len = None
    def step(self, value):
        # 如果 self.min_len 还未赋值，直接将当前 value 赋值给 self.min_lin
        if self.min_len is None :
            self.min_len = value
            return
        # 找到一个长度更短的 value，用 value 代替 self.min_len
        if len(self.min_len) > len(value):
            self.min_len = value
    def finalize(self):
        return self.min_len
# ①、打开或创建数据库
# 也可以使用特殊名：:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# 调用 create_aggregate 注册自定义聚集函数：min_len
conn.create_aggregate('min_len', 1, MinLen)
# ②、获取游标
c = conn.cursor()
# ③、在 SQL 语句中使用 min_len 自定义聚集函数
c.execute('select min_len(pass) from user_tb')
print(c.fetchone()[0])
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

程序中第 22 行代码使用 create_aggregate() 创建了一个自定义聚集函数，该函数用于将 MinLen 类注册成 min_len 自定义聚集函数，其中 MinLen 类中的 step() 方法负责对每个传入的参数进行比较，选出长度最短的字符串，而 finalize() 方法则负责返回长度最短的字符串，该方法的返回值将作为聚集函数的返回值；第 26 行代码在 select 语句中使用自定义聚集函数，通过该函数就可以选出长度最短的密码。

运行上面的程序，将可以看到 use_tb 表中长度最短的密码被选出来。