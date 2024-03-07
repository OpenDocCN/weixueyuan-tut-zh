# MySQL REPLACE 函数：字符串替换

> 原文：[`c.biancheng.net/mysql/replace.html`](http://c.biancheng.net/mysql/replace.html)

MySQL 中替换函数 REPLACE(s，s1，s2) 使用字符串 s2 替换字符串 s 中所有的字符串 s1。

【实例】使用 REPLACE 函数进行字符串替换操作，输入的 SQL 语句和执行过程如下所示。

```

mysql> SELECT REPLACE('aaa.mysql.com','a','w');
+----------------------------------+
| REPLACE('aaa.mysql.com','a','w') |
+----------------------------------+
| www.mysql.com                    |
+----------------------------------+
1 row in set (0.00 sec)
```

由运行结果可以看出，使用 REPLACE('aaa.mysql.com'，'a'，'w') 将“aaa.mysql.com”字符串的“a”字符替换为“w”字符，结果为“www.mysql.com”。