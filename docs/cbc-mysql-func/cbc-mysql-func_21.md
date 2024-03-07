# MySQL INSERT 函数：替换字符串

> 原文：[`c.biancheng.net/mysql/insert.html`](http://c.biancheng.net/mysql/insert.html)

MySQL 中替换字符串函数 INSERT(s1，x，len，s2) 返回字符串 s1，子字符串起始于 x 位置，并且用 len 个字符长的字符串代替 s2。

若 x 超过字符串长度，则返回值为原始字符串。假如 len 的长度大于其他字符串的长度，则从位置 x 开始替换。若任何一个参数为 NULL，则返回值为 NULL。

【实例】使用 INSERT 函数进行字符串替换操作，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT INSERT('Football',2,4,'Play') AS col1,
    -> INSERT('Football',-1,4,'Play') AS col2,
    -> INSERT('Football',3,20,'Play') AS col3;
+----------+----------+--------+
| col1     | col2     | col3   |
+----------+----------+--------+
| FPlayall | Football | FoPlay |
+----------+----------+--------+
1 row in set (0.04 sec)
```

由执行结果可知：

*   第一个函数 INSERT('Football'，2，4，'Play') 将“Football”从第 2 个字符开始长度为 4 的字符串替换为 Play，结果为“FPlayall”；
*   第二个函数 ('Football'，-1，4，'Play') 中的起始位置 -1 超出了字符串长度，直接返回原字符串；
*   第三个函数 INSERT('Football'，3，20，'Play') 替换长度超出了原字符串长度，则从第 3 个字符开始，截取后面所有的字符，并替换为指定字符 Play，结果为“FoPlay”。