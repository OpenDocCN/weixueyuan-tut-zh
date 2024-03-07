# MySQL CURDATE 和 CURRENT_DATE 函数：获取系统当前日期

> 原文：[`c.biancheng.net/mysql/curdate_current_date.html`](http://c.biancheng.net/mysql/curdate_current_date.html)

MySQL 中 CURDATE() 和 CURRENT_DATE() 函数的作用相同，将当前日期按照“YYYY-MM-DD”或“YYYYMMDD”格式的值返回，具体格式根据函数用在字符串或数字语境中而定。

【实例】使用日期函数 CURDATE 和 CURRENT_DATE 获取系统当前日期，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT CURDATE(),CURRENT_DATE(),CURRENT_DATE()+0;
+------------+----------------+------------------+
| CURDATE()  | CURRENT_DATE() | CURRENT_DATE()+0 |
+------------+----------------+------------------+
| 2017-04-01 | 2017-04-01     |         20170401 |
+------------+----------------+------------------+
1 row in set (0.03 sec)
```

由运行结果可以看到，两个函数的作用相同，返回了相同的系统当前日期，“CURDATE()+0”将当前日期值转换为数值型的。