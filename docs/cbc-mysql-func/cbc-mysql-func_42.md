# MySQL DAYOFMONTH 函数：获取指定日期在一个月的位置

> 原文：[`c.biancheng.net/mysql/dayofmonth.html`](http://c.biancheng.net/mysql/dayofmonth.html)

MySQL 中 DAYOFMONTH(d) 函数返回 d 是一个月中的第几天，范围为 1～31。

【实例】使用 DAYOFMONTH() 函数返回指定日期在一个月中的位置，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT DAYOFMONTH('2017-12-15');
+--------------------------+
| DAYOFMONTH('2017-12-15') |
+--------------------------+
|                       15 |
+--------------------------+
1 row in set (0.02 sec)
```

由运行结果可以看出，2017-12-15 是这个月的第 15 天。