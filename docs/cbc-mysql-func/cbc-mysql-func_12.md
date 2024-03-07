# MySQL ASIN 函数：求反正弦值

> 原文：[`c.biancheng.net/mysql/asin.html`](http://c.biancheng.net/mysql/asin.html)

MySQL 中反正弦函数 ASIN(x) 返回 x 的反正弦值，若 x 不在 -1 到 1 的范围之内，则返回 NULL。

【实例】使用 ASIN 函数计算反正弦值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT ASIN(0.8414709848078965),ASIN(2);
+--------------------------+---------+
| ASIN(0.8414709848078965) | ASIN(2) |
+--------------------------+---------+
|                        1 |    NULL |
+--------------------------+---------+
1 row in set (0.03 sec)
```

由结果可以看到，函数 ASIN 和 SIN 互为反函数，ASIN(2) 中的参数 2 超出了正弦值的范围，因此返回 NULL。