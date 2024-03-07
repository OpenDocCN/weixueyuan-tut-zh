# Python MySQL callproc 方法：调用数据库存储过程

MySQL 数据库模块为游标对象提供了一个非标准的 `callproc(self, procname, args=())` 方法，该方法用于调用数据库存储过程。

callproc 方法的 procname 参数代表存储过程的名字，而 args 参数则用于为存储过程传入参数。

下面的 SQL 脚本可以在 MySQL 数据库中创建一个简单的存储过程。打开 MySQL 的命令行客户端，连接 python 数据库之后，输入如下 SQL 脚本来创建存储过程：

delimiter //
create procedure add pro(a int, b int, out sum int)
begin
set sum = a + b;
end;
//

下面程序示范了使用 MySQL 数据库模块来调用存储过程：

```
# 导入访问 MySQL 的模块
import mysql.connector

# ①、连接数据库
conn = conn = mysql.connector.connect(user='root', password='32147',
    host='localhost', port='3306',
    database='python', use_unicode=True)
# ②、获取游标
c = conn.cursor()
# ③、调用 callproc()方法执行存储过程
# 虽然 add_pro 存储过程需要 3 个参数，但最后一个参数是传出参数，
# 因此程序不会用它的值
result_args = c.callproc('add_pro', (5, 6, 0))
# 返回的 result_args 既包含了传入参数的值，也包含了传出参数的值
print(result_args)
# 如果只想访问传出参数的值，可直接访问 result_args 的第 3 个元素，如下代码
print(result_args[2])
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序中，第 13 行代码就是调用存储过程的关键代码。使用 MySQL 数据库模块调用存储过程非常简单，存储过程需要几个参数，程序通过 callproc() 方法调用存储过程时就传入一个包含几个元素的元组；对于存储过程的传入参数，该参数对应的元组元素负责为传入参数传值；对于存储过程的传出参数，该参数对应的元组元素随便定义即可。

运行上面的程序，可以看到如下输出结果：

(5, 6, 11)
11

从上面的输出结果来看，当程序使用 Python 调用存储过程后，程序会返回传入参数和传出参数组成的元组，如第一行输出结果所示。如果程序只需要获取传出参数的值，则通过返回的结果元组取出对应的值即可。