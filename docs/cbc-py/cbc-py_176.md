# Python SQLite create_collation 方法：创建自定义比较函数

在标准的 SQL 语句中提供了一个 order by 子句，该子句用于对查询结果进行排序，但这种排序只会按默认的排序规则进行，如果程序需要按业务相关规则进行排序，则需要创建自定义的比较函数。

如果程序需要在 SQL 语句中使用与业务相关的比较函数，则可使用数据库连接对象所提供的 `create_collation(name, callable)`方法，该方法用于注册一个自定义的比较函数。

create_collation 方法包含两个参数：

1.  name：指定自定义比较函数的名字。
2.  callable：指定自定义比较函数对应的函数。该函数包含两个参数，并对这两个参数进行大小比较，如果该方法返回正整数，系统认为第一个参数更大；如果返回负整数，系统认为第二个参数更大；如果返回 0，系统认为两个参数相等。

需要注意的是，callable 函数的参数以 Python（bytes）字节串的形式传入，因此系统默认会以 UTF-8 字符集将字符串编码成字节串后传入 callable 函数。

假设要求对 user_tb 表中的 pass 进行排序，但考虑到 pass 列前面采用了加密：第一个字符和最后一个字符都是方括号，因此程序会对 pass 列去掉前后两个方括号之后再进行排序。所以，程序需要自定义比较函数，该函数将会把字符串的第一个字符和最后一个字符去掉后比较大小。

下面程序创建了一个自定义比较函数，然后在 SQL 语句中使用该自定义比较函数进行排序：

```
# 导入访问 SQLite 的模块
import sqlite3

# 去掉字符串第一个、最后一个字符后比较大小
def my_collate(st1, st2):
    if st1[1: -1] == st2[1: -1]:
        return 0
    elif st1[1: -1] > st2[1: -1]:
        return 1
    else:
        return -1
# ①、打开或创建数据库
# 也可以使用特殊名：:memory:代表创建内存中的数据库
conn = sqlite3.connect('first.db')
# 调用 create_collation 注册自定义比较函数：sub_cmp
conn.create_collation('sub_cmp', my_collate)
# ②、获取游标
c = conn.cursor()
# ③、在 SQL 语句中使用 sub_cmp 自定义的比较函数
c.execute('select * from user_tb order by pass collate sub_cmp')
# 采用 for 循环遍历游标
for row in c:
    print(row)
conn.commit()
# ④、关闭游标
c.close()
# ⑤、关闭连接
conn.close()
```

上面程序中，第 5 行代码定义了一个大小比较函数：my_collate()，该函数会去掉字节串的第一个字符和最后一个字符后再比较大小。第 16 行代码调用 create_collation() 方法将 my_collate() 函数注册为 sub_cmp 自定义比较函数，接下来就可以在 SQL 语句中使用该自定义比较函数，如第 20 行代码所示。

运行上面的程序，将可以看到查询结果是按 pass 列去掉前后各一个字符之后进行排序的。

上面程序还用到游标的一个特点，游标本身是可迭代对象，因此程序不需要使用 fetchone() 来逐行获取查询结果，而是直接使用 for 循环来遍历游标获取查询结果集。