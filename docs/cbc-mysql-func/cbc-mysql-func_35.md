# MySQL FROM_UNIXTIME 函数：时间戳转日期

> 原文：[`c.biancheng.net/mysql/from_unixtime.html`](http://c.biancheng.net/mysql/from_unixtime.html)

MySQL FROM_UNIXTIME(date) 函数把 UNIX 时间戳转换为普通格式的日期时间值，与 UNIX_TIMESTAMP () 函数互为反函数。

【实例】使用 FROM_UNIXTIME(date) 函数将 UNIX 时间戳转换为普通格式时间。

```

mysql> SELECT FROM_UNIXTIME(1150051270);
+---------------------------+
| FROM_UNIXTIME(1150051270) |
+---------------------------+
| 2006-06-12 02:41:10       |
+---------------------------+
1 row in set (0.00 sec)
```