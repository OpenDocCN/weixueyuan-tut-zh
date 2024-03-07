# MySQL DAYOFWEEK 函数：获取日期对应的周索引

> 原文：[`c.biancheng.net/mysql/dayofweek.html`](http://c.biancheng.net/mysql/dayofweek.html)

MySQL 中 DAYOFWEEK(d) 函数返回 d 对应的一周中的索引（位置）。1 表示周日，2 表示周一，……，7 表示周六。这些索引值对应于 ODBC 标准。

【实例】使用 DAYOFWEEK() 函数返回日期对应的周索引，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT DAYOFWEEK('2017-12-15');
+-------------------------+
| DAYOFWEEK('2017-12-15') |
+-------------------------+
|                       6 |
+-------------------------+
1 row in set (0.04 sec)
```

由运行结果可知，2017 年 12 月 15 日为周五，因此返回其对应的索引值为 6。