# MySQL DATEDIFF 函数：获取两个日期的时间间隔

> 原文：[`c.biancheng.net/mysql/datediff.html`](http://c.biancheng.net/mysql/datediff.html)

MySQL 中 DATEDIFF(date1，date2) 返回起始时间 date1 和结束时间 date2 之间的天数。date1 和 date2 为日期或 date-and-time 表达式。计算时只用到这些值的日期部分。

【实例】使用 DATEDIFF() 函数计算两个日期之间的间隔天数，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT DATEDIFF('2017-11-30','2017-11-29') AS COL1,
    -> DATEDIFF('2017-11-30','2017-12-15') AS col2;
+------+------+
| COL1 | col2 |
+------+------+
|    1 |  -15 |
+------+------+
1 row in set (0.00 sec)
```

由运行结果可知，DATEDIFF() 函数返回 date1-date2 后的值，因此 DATEDIFF('2017-11-30'，'2017-11-29') 的返回值为 1，DATEDIFF('2017-11-30 23：59：59'，'2017-12-15') 的返回值为 -15。