# MySQL YEAR 函数：获取年份

> 原文：[`c.biancheng.net/mysql/year.html`](http://c.biancheng.net/mysql/year.html)

MySQL YEAR() 函数可以从指定日期值中来获取年份值。

YEAR() 函数需要接受 date 参数，并返回日期的年份。语法格式如下：

YEAR(date);

YEAR() 函数返回的年份值范围为 1000 到 9999，如果日期为零，YEAR() 函数返回 0。

【实例】使用 YEAR() 函数获取当前时间的年份，输入 SQL 语句和执行结果如下。

```

mysql> SELECT YEAR(NOW());
+-------------+
| YEAR(NOW()) |
+-------------+
|        2019 |
+-------------+
1 row in set (0.00 sec)
```