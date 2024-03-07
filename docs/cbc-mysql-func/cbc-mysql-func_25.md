# MySQL RIGHT 函数：从右侧截取字符串

> 原文：[`c.biancheng.net/mysql/right.html`](http://c.biancheng.net/mysql/right.html)

MySQL 中的 RIGHT(s，n) 函数返回字符串 s 最右边的 n 个字符。

【实例】使用 RIGHT 函数返回字符串中右边的字符，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT RIGHT('MySQL',3);
+------------------+
| RIGHT('MySQL',3) |
+------------------+
| SQL              |
+------------------+
1 row in set (0.00 sec)
```

由执行结果可知，函数返回字符串“MySQL”右边开始的长度为 3 的子字符串，结果为“SQL”。

如果要获取字符串最左边的若干个字符，请参考 LEFT 函数