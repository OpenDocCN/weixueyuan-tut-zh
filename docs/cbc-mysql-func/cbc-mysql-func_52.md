# MySQL WEEKDAY 函数：获取指定日期在一周内的索引位置

> 原文：[`c.biancheng.net/mysql/weekday.html`](http://c.biancheng.net/mysql/weekday.html)

MySQL 中 WEEKDAY(d) 返回 d 对应的工作日索引。0 表示周一，1 表示周二，……，6 表示周日。

【实例】使用 WEEKDAY() 函数返回日期对应的工作日索引，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT WEEKDAY('2017-12-15');
+-----------------------+
| WEEKDAY('2017-12-15') |
+-----------------------+
|                     4 |
+-----------------------+
1 row in set (0.00 sec)
```

由运行结果可以看出，WEEKDAY() 和 DAYOFWEEK() 函数都是返回指定日期在某一周内的位置，只是索引编号不同。