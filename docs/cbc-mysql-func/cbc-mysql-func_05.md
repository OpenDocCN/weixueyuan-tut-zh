# MySQL CEIL 和 CELING 函数：向上取整

> 原文：[`c.biancheng.net/mysql/ceil_celing.html`](http://c.biancheng.net/mysql/ceil_celing.html)

在 MySQL 中取整函数 CEIL(x) 和 CEILING(x) 的意义相同，返回不小于 x 的最小整数值，返回值转化为一个 BIGINT。

【实例】使用 CEILING 函数返回最小整数，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT CEIL(-2.5),CEILING(2.5);
+------------+--------------+
| CEIL(-2.5) | CEILING(2.5) |
+------------+--------------+
|         -2 |            3 |
+------------+--------------+
1 row in set (0.00 sec)
```

由执行结果可知，-2.5 为负数，不小于 -2.5 的最小整数为 -2，因此返回值为 -2；不小于 2.5 的最小整数为 3，因此返回值为 3。