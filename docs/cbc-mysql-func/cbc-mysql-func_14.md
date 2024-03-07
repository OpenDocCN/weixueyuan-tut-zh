# MySQL ACOS 函数：求反余弦值

> 原文：[`c.biancheng.net/mysql/acos.html`](http://c.biancheng.net/mysql/acos.html)

MySQL 中反余弦函数 ACOS(x) 。x 值的范围必须在 -1 和 1 之间，否则返回 NULL。

【实例】使用 ACOS 函数计算反余弦值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT ACOS(2),ACOS(1),ACOS(-1);
+---------+---------+-------------------+
| ACOS(2) | ACOS(1) | ACOS(-1)          |
+---------+---------+-------------------+
|    NULL |       0 | 3.141592653589793 |
+---------+---------+-------------------+
1 row in set (0.01 sec)
```

由结果可以看到，函数 ACOS 和 COS 互为反函数。