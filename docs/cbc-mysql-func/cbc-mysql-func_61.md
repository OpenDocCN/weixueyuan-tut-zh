# MySQL IFNULL 函数：判断是否为空

> 原文：[`c.biancheng.net/mysql/ifnull.html`](http://c.biancheng.net/mysql/ifnull.html)

MySQL IFNULL 函数是 MySQL 控制流函数之一，它接受两个参数，如果不是 NULL，则返回第一个参数。 否则，IFNULL 函数返回第二个参数。两个参数可以是文字值或表达式。

函数的语法：

IFNULL(v1,v2);

其中：如果 v1 不为 NULL，则 IFNULL 函数返回 v1; 否则返回 v2 的结果。

【实例】使用 IFNULL(v1,v2) 函数根据 v1 的取值返回相应值。输入 SQL 语句和执行结果如下。

```

mysql> SELECT IFNULL(5,8),IFNULL(NULL,'OK'),IFNULL(SQRT(-8),'FALSE'),SQRT(-8);
+-------------+-------------------+--------------------------+----------+
| IFNULL(5,8) | IFNULL(NULL,'OK') | IFNULL(SQRT(-8),'FALSE') | SQRT(-8) |
+-------------+-------------------+--------------------------+----------+
|           5 | OK                | FALSE                    |     NULL |
+-------------+-------------------+--------------------------+----------+
1 row in set (0.00 sec)
```

由执行结果可以看出，IFNULL(v1,v2) 函数中的参数 v1=5、v2=8，都不为空，即 v1=5 不为空，返回 v1 的值为 5；当 v1=NULL 时，返回 v2 的值，即字符串 'OK'；当 v1=SQRT(-8) 时，SQRT(-8) 函数的返回值为 NULL，即 v1=NULL，所以返回 v2 为字符串 'false'。