# MySQL CONCAT 函数：字符串拼接

> 原文：[`c.biancheng.net/mysql/concat.html`](http://c.biancheng.net/mysql/concat.html)

MySQL 中的 CONCAT(sl，s2，...) 函数返回结果为连接参数产生的字符串，或许有一个或多个参数。

若有任何一个参数为 NULL，则返回值为 NULL。若所有参数均为非二进制字符串，则结果为非二进制字符串。若自变量中含有任一二进制字符串，则结果为一个二进制字符串。

【实例】使用 CONCAT 函数连接字符串，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT CONCAT('MySQL','5.7'),CONCAT('MySQL',NULL);
+-----------------------+----------------------+
| CONCAT('MySQL','5.7') | CONCAT('MySQL',NULL) |
+-----------------------+----------------------+
| MySQL5.7              | NULL                 |
+-----------------------+----------------------+
1 row in set (0.03 sec)
```

由运行结果可知，CONCAT('MySQL'，'5.7') 返回两个字符串连接后的字符串；CONCAT(‘MySQL’，NULL) 中有一个参数为 NULL，因此返回结果为 NULL。