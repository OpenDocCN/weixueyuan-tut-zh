# MySQL SEC_TO_TIME 函数：将秒值转换为时间格式

> 原文：[`c.biancheng.net/mysql/sec_to_time.html`](http://c.biancheng.net/mysql/sec_to_time.html)

MySQL SEC_TO_TIME(seconds) 函数返回将参数 seconds 转换为小时、分钟和秒数的时间值。

【实例】使用 SEC_TO_TIME(seconds) 函数将秒值转换为时间格式，输入 SQL 语句和执行结果如下。

```

mysql> SELECT SEC_TO_TIME('54925');
+----------------------+
| SEC_TO_TIME('54925') |
+----------------------+
| 15:15:25             |
+----------------------+
1 row in set (0.00 sec)
```

由执行结果可以看出，将上例中得到的秒数 54925 通过 SEC_TO_TIME(seconds) 函数计算，返回结果是时间值 15:15:25，为字符串型。