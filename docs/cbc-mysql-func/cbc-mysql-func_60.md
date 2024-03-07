# MySQL IF 函数：判断

> 原文：[`c.biancheng.net/mysql/if.html`](http://c.biancheng.net/mysql/if.html)

MySQL IF 语句允许您根据表达式的某个条件或值结果来执行一组 SQL 语句。

要在 MySQL 中形成一个表达式，可以结合文字，变量，运算符，甚至函数来组合。表达式可以返回 TRUE,FALSE 或 NULL，这三个值之一。

语法结构如下：

IF(expr,v1,v2)

其中：表达式 expr 得到不同的结果，当 expr 为真是返回 v1 的值，否则返回 v2.

【实例】使用 IF(expr,v1,v2) 函数根据 expr 表达式结果返回相应值，输入 SQL 语句和执行结果如下。

```

mysql> SELECT IF(1<2,1,0) c1,IF(1>5,'√','×') c2,IF(STRCMP('abc','ab'),'yes','no') c3;
+----+----+-----+
| c1 | c2 | c3  |
+----+----+-----+
|  1 | × | yes |
+----+----+-----+
1 row in set, 2 warnings (0.00 sec)
```

由执行结果可以看出，在 c1 中，表达式 1<2 所得的结果是 TRUE，则返回结果为 v1，即数值 1；在 c2 中，表达式 1>5 所得的结果是 FALSE，则返回结果为 v2，即字符串 '×'；在 c3 中，先用 STRCMP(s1,s2) 函数比较两个字符串的大小，字符串 'abc' 和 'ab' 比较结果的返回值为 1，也就是表达式 expr 的返回结果不等于 0 且不等于 NULL，则返回值为 v1，即字符串 'yes'。