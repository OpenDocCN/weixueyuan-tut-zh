# MySQL TRIM 函数：删除空格

> 原文：[`c.biancheng.net/mysql/trim.html`](http://c.biancheng.net/mysql/trim.html)

MySQL 中删除空格函数 TRIM(s) 删除字符串 s 两侧的空格。

【实例】SELECT CONCAT('['，TRIM('mobile')，']')；输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT '[   mobile   ]',CONCAT('[',TRIM('   mobile   '),']');
+----------------+--------------------------------------+
| [   mobile   ] | CONCAT('[',TRIM('   mobile   '),']') |
+----------------+--------------------------------------+
| [   mobile   ] | [mobile]                             |
+----------------+--------------------------------------+
1 row in set (0.07 sec)
```

由执行结果可知，函数执行之后字符串“mobile”两边的空格被删除，结果为“mobile”。