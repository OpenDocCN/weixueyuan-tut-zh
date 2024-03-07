# MySQL ATAN 函数：求反正切值

> 原文：[`c.biancheng.net/mysql/atan.html`](http://c.biancheng.net/mysql/atan.html)

MySQL 中反正切 ATAN(x) 返回 x 的反正切值，正切为 x 的值。

【实例】使用 ATAN 函数计算反正切值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT ATAN(1.5574077246549023),ATAN(0);
+--------------------------+---------+
| ATAN(1.5574077246549023) | ATAN(0) |
+--------------------------+---------+
|                        1 |       0 |
+--------------------------+---------+
1 row in set (0.05 sec)
```

由结果可以看到，函数 ATAN 和 TAN 互为反函数。