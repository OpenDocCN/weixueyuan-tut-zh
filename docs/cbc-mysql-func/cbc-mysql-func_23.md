# MySQL UPPER 函数：将字母转换成大写

> 原文：[`c.biancheng.net/mysql/upper.html`](http://c.biancheng.net/mysql/upper.html)

MySQL 中字母大写转换函数 UPPER(str) 可以将字符串 str 中的字母字符全部转换成大写。

【实例】使用 UPPER 函数将字符串中所有的字母字符转换为大写，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT UPPER('green'),UPPER('Green');
+----------------+----------------+
| UPPER('green') | UPPER('Green') |
+----------------+----------------+
| GREEN          | GREEN          |
+----------------+----------------+
1 row in set (0.03 sec)
```

由结果可以看到，原来所有字母字符为小写的，全部转换为大写，如“green”，转换之后为“GREEN”；大小写字母混合的字符串，大写不变，小写字母转换为大写字母，如“Green”，转换之后为“GREEN”。

如果想将字符串中的字母字符装换为小写，请参考 LOWER 函数