# MySQL LOWER 函数：将字母转换成小写

> 原文：[`c.biancheng.net/mysql/lower.html`](http://c.biancheng.net/mysql/lower.html)

MySQL 中字母小写转换函数 LOWER(str) 可以将字符串 str 中的字母字符全部转换成小写。

【实例】使用 LOWER 函数将字符串中所有的字母字符转换为小写，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT LOWER('BLUE'),LOWER('Blue');
+---------------+---------------+
| LOWER('BLUE') | LOWER('Blue') |
+---------------+---------------+
| blue          | blue          |
+---------------+---------------+
1 row in set (0.03 sec)
```

由结果可以看到，原来所有字母为大写的，全部转换为小写，如“BLUE”，转换之后为“blue”；大小写字母混合的字符串，小写不变，大写字母转换为小写字母，如“Blue”，转换之后为“bule”。

如果想将字符串中的字母字符装换为大写，请参考 UPPER 函数