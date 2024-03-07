# MySQL REVERSE 函数：反转字符串

> 原文：[`c.biancheng.net/mysql/reverse.html`](http://c.biancheng.net/mysql/reverse.html)

MySQL 中字符串逆序函数 REVERSE(s) 可以将字符串 s 反转，返回的字符串的顺序和 s 字符串的顺序相反。

【实例】使用 REVERSE 函数反转字符串，输入的 SQL 语句和执行过程如下所示。

```

mysql> SELECT REVERSE('hello');
+------------------+
| REVERSE('hello') |
+------------------+
| olleh            |
+------------------+
1 row in set (0.00 sec)
```

由运行结果可以看出，字符串“hello”经过 REVERSE 函数处理之后所有字符顺序被反转，结果为“olleh”。