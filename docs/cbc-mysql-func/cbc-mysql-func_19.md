# MySQL LENGTH 函数：获取字符串长度

> 原文：[`c.biancheng.net/mysql/length.html`](http://c.biancheng.net/mysql/length.html)

MySQL LENGTH(str) 函数的返回值为字符串的字节长度，使用 uft8（UNICODE 的一种变长字符编码，又称万国码）编码字符集时，一个汉字是 3 个字节，一个数字或字母是一个字节。

【实例】使用 LENGTH 函数计算字符串长度，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT LENGTH('name'),LENGTH('数据库');
+----------------+---------------------+
|LENGTH('name')  | LENGTH('数据库')    |
+----------------+---------------------+
|              4 |                   9 |
+----------------+---------------------+
1 row in set (0.04 sec)
```

由运行结果可以看到，一个汉字是 3 个字节，“数据库”字符串占 9 个字节。英文字符的个数和所占的字节相同，一个字符占 1 个字节。