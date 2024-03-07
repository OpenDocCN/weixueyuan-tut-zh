# MySQL TAN 函数：求正切值

> 原文：[`c.biancheng.net/mysql/tan.html`](http://c.biancheng.net/mysql/tan.html)

MySQL 中正切函数 TAN(x) 返回 x 的正切值，x 为给定的弧度值。

【实例】使用 TAN 函数计算正切值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT TAN(1),TAN(0);
+--------------------+--------+
| TAN(1)             | TAN(0) |
+--------------------+--------+
| 1.5574077246549023 |      0 |
+--------------------+--------+
1 row in set (0.03 sec)
```

由运行结果可知，TAN(1) 的值为 1.5574077246549023，TAN(0) 的值为 0。