# MySQL DAYOFYEAR 函数：获取指定日期在一年中的位置

> 原文：[`c.biancheng.net/mysql/dayofyear.html`](http://c.biancheng.net/mysql/dayofyear.html)

MySQL 中 DAYOFYEAR(d) 函数返回 d 是一年中的第几天，范围为 1～366。

【实例】使用 DAYOFYEAR() 函数返回指定日期在一年中的位置，输入的 SQL 语句和执行过程如下所示。

```

mysql> SELECT DAYOFYEAR('2017-12-15');
+-------------------------+
| DAYOFYEAR('2017-12-15') |
+-------------------------+
|                     349 |
+-------------------------+
1 row in set (0.00 sec)
```

由运行结果可以看出，1、3、5、7、8、10 月份各 31 天，2 月份各 28 天，4、6、9、11 月份各 30 天，再加上 12 月份 15 天，因此返回结果为 349。