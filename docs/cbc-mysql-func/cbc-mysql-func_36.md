# MySQL MONTH 函数：获取指定日期的月份

> 原文：[`c.biancheng.net/mysql/month.html`](http://c.biancheng.net/mysql/month.html)

MySQL 中 MONTH(date) 函数返回指定 date 对应的月份，范围为 1～12。

【实例】使用 MONTH() 函数返回指定日期中的月份，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT MONTH('2017-12-15');
+---------------------+
| MONTH('2017-12-15') |
+---------------------+
|                  12 |
+---------------------+
1 row in set (0.00 sec)
```

由运行结果可知，2017-12-15 对应的月份是 12。