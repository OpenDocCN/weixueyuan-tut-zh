# MySQL LEFT 函数：从左侧截取字符串

> 原文：[`c.biancheng.net/mysql/left.html`](http://c.biancheng.net/mysql/left.html)

MySQL 中的 LEFT(s，n) 函数返回字符串 s 最左边的 n 个字符。

【实例】使用 LEFT 函数返回字符串中左边的字符，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT LEFT('MySQL',2);
+-----------------+
| LEFT('MySQL',2) |
+-----------------+
| My              |
+-----------------+
1 row in set (0.04 sec)
```

由运行结果可知，返回字符串“MySQL”左边开始的长度为 2 的子字符串，结果为“My”。

如果要获取字符串最右边的若干个字符，请参考 RIGHT 函数