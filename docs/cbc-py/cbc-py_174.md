# Python SQLite create_function 方法：注册自定义函数

数据库连接对象还提供了一个 create_function(name, num_params, func) 方法，该方法用于注册一个自定义函数，接下来程序就可以在 SQL 语句中使用该自定义函数：

create_function 方法包含 3 个参数：

*   name 参数：指定注册的自定义函数的名字。
*   num_params：指定自定义函数所需参数的个数。
*   func：指定自定义函数对应的函数。

下面程序使用 create_function() 方法为 SQL 语句注册一个自定义函数，然后程序就可以在 SQL 语句中使用该自定义函数：

```
# 导入访问 SQLite 的模块
import sqlite3

# 先定义一个普通函数，准备注册为 SQL 中的自定义函数
def reverse_ext(st):
    # 对字符串反转，前后加方括号
    return '[' + st[::-1] + ']'
# ①、打开或创建数据库
# 也可以使用特殊名：:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# 调用 create_function 注册自定义函数：enc
conn.create_function('enc', 1, reverse_ext)
# ②、获取游标
c = conn.cursor()
# ③、在 SQL 语句中使用 enc 自定义函数
c.execute('insert into user_tb values(null, ?, enc(?), ?)',
    ('贾宝玉', '123456', 'male'))
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序中，第 12 行代码将 reverse_ext() 函数注册为自定义函数 enc，该函数用于模拟一个简单的加密功能：程序会对字符串反转，并在字符串前后添加方括号。

提示：此时使用的加密功能只是一个简单的模拟，如果需要真正对密码进行加密，则建议使用更高强度的加密算法，比如加盐 MD5 加密。

程序中第 16 行代码在执行的 SQL 语句中使用了 enc 自定义函数，该自定义函数用于对插入的密码进行加密。因此，当使用上面程序插入数据时，程序会自动对插入的密码进行加密；程序会对密码进行反转，并在密码前后添加方括号。