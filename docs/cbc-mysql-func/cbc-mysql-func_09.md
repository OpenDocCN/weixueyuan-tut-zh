# MySQL SIGN 函数：返回参数的符号

> 原文：[`c.biancheng.net/mysql/sign.html`](http://c.biancheng.net/mysql/sign.html)

在 MySQL 中符号函数 SIGN(x) 返回参数的符号，x 的值为负、零和正时返回结果依次为 -1、0 和 1。

【实例】使用 SIGN 函数返回参数的符号，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT SIGN(-6),SIGN(0),SIGN(34);
+----------+---------+----------+
| SIGN(-6) | SIGN(0) | SIGN(34) |
+----------+---------+----------+
|       -1 |       0 |        1 |
+----------+---------+----------+
1 row in set (0.00 sec)
```

由执行结果可知，SIGN(-6) 返回 -1，SIGN(0) 返回 0，SIGN(34) 返回 1。