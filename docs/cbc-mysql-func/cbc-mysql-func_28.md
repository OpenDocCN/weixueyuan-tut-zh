# MySQL SUBSTRING 函数：截取字符串

> 原文：[`c.biancheng.net/mysql/substring.html`](http://c.biancheng.net/mysql/substring.html)

MySQL 中获取子串函数 SUBSTRING(s，n，len) 带有 len 参数的格式，从字符串 s 返回一个长度同 len 字符相同的子字符串，起始于位置 n。

也可能对 n 使用一个负值。假若这样，则子字符串的位置起始于字符串结尾的第 n 个字符，即倒数第 n 个字符，而不是字符串的开头位置。

【实例】使用 SUBSTRING 函数获取指定位置处的子字符串，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT SUBSTRING('computer',3) AS col1,
    -> SUBSTRING('computer',3,4) AS col2,
    -> SUBSTRING('computer',-3) AS col3,
    -> SUBSTRING('computer',-5,3) AS col4;
+--------+------+------+------+
| col1   | col2 | col3 | col4 |
+--------+------+------+------+
| mputer | mput | ter  | put  |
+--------+------+------+------+
1 row in set (0.00 sec)
```

SUBSTRING('computer'，3) 返回从第 3 个位置开始到字符串结尾的子字符串，结果为“mputer”；SUBSTRING('computer'，3，4) 返回从第 3 个位置开始长度为 4 的子字符串，结果为“mput”；

SUBSTRING(computer，-3) 返回从倒数第 3 个位置到字符串结尾的子字符串，结果为“ter”；SUBSTRING(computer，-5，3) 返回从倒数第 5 个位置开始长度为 3 的子字符串，结果为“put”。