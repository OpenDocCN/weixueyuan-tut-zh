# MySQL COT 函数：求余切值

> 原文：[`c.biancheng.net/mysql/cot.html`](http://c.biancheng.net/mysql/cot.html)

MySQL 余切函数 COT(x) 返回 x 的余切值，x 是给定的弧度值。

【实例】使用 COT 函数计算余切值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT COT(1);
+--------------------+
| COT(1)             |
+--------------------+
| 0.6420926159343306 |
+--------------------+
1 row in set (0.00 sec)
```

由运行结果可知，COT(1) 的值为 0.6420926159343306。