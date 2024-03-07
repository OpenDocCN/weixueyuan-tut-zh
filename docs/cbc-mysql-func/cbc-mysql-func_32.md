# MySQL CURTIME 和 CURRENT_TIME 函数：获取系统当前时间

> 原文：[`c.biancheng.net/mysql/curtime_current_time.html`](http://c.biancheng.net/mysql/curtime_current_time.html)

MySQL 中 CURTIME() 和 CURRENT_TIME() 函数的作用相同，将当前时间以“HH：MM：SS”或“HHMMSS”格式返回，具体格式根据函数用在字符串或数字语境中而定。

【实例】使用时间函数 CURTIME 和 CURRENT_TIME 获取系统当前时间，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT CURTIME(),CURRENT_TIME(),CURRENT_TIME()+0;
+-----------+----------------+------------------+
| CURTIME() | CURRENT_TIME() | CURRENT_TIME()+0 |
+-----------+----------------+------------------+
| 19:39:51  | 19:39:51       |           193951 |
+-----------+----------------+------------------+
1 row in set (0.04 sec)
```

由运行结果可以看出，两个函数返回的结果相同，都返回了当前的系统时间。CURRENT_TIME()+0 是将当前日期值转换为数值型的。