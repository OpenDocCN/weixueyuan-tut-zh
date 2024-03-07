# MySQL UNIX_TIMESTAMP 函数：获取 UNIX 时间戳

> 原文：[`c.biancheng.net/mysql/unix_timestamp.html`](http://c.biancheng.net/mysql/unix_timestamp.html)

MySQL UNIX_TIMESTAMP(date) 若无参数调用，返回一个无符号整数类型的 UNIX 时间戳（'1970-01-01 00:00:00'GMT 之后的秒数）。

若用 date 来调用 UNIX_TIMESTAMP()，它会将参数值以'1970-01-01 00:00:00'GMT 后的秒数的形式返回。

【实例】使用 UNIX_TIMESTAMP() 函数返回 UNIX 格式的时间戳，输入 SQL 语句和执行结果如下。

```

mysql> SELECT UNIX_TIMESTAMP(),UNIX_TIMESTAMP(NOW()),NOW();
+------------------+-----------------------+---------------------+
| UNIX_TIMESTAMP() | UNIX_TIMESTAMP(NOW()) | NOW()               |
+------------------+-----------------------+---------------------+
|       1551251270 |            1551251270 | 2019-02-27 15:07:50 |
+------------------+-----------------------+---------------------+
1 row in set (0.00 sec)
```