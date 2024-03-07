# MySQL MONTHNAME 函数：获取指定日期月份的英文名称

> 原文：[`c.biancheng.net/mysql/monthname.html`](http://c.biancheng.net/mysql/monthname.html)

MySQL 中 MONTHNAME(date) 函数返回日期 date 对应月份的英文全名。

【实例】使用 MONTHNAME() 函数返回指定日期中月份的名称，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT MONTHNAME('2017-12-15');
+-------------------------+
| MONTHNAME('2017-12-15') |
+-------------------------+
| December                |
+-------------------------+
1 row in set (0.00 sec)
```

由运行结果可知，2017-12-15 对应月份的英文全名是 December。