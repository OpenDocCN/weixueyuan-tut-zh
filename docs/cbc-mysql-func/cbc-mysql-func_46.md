# MySQL DATE_ADD 和 ADDDATE 函数：向日期添加指定时间间隔

> 原文：[`c.biancheng.net/mysql/date_add_adddate.html`](http://c.biancheng.net/mysql/date_add_adddate.html)

MySQL DATE_ADD(date,INTERVAL expr type) 和 ADDDATE(date,INTERVAL expr type) 两个函数的作用相同，都是用于执行日期的加运算。

DATE_ADD() 和 ADDDATE() 函数有两个参数：

*   date 是 DATE 或 DATETIME 的起始值。
*   INTERVAL expr type 是要添加到起始日期值的间隔值。

【实例 1】使用 DATE_ADD(date,INTERVAL expr type) 函数执行日期的加运算。输入 SQL 语句和执行结果如下。

```

mysql> SELECT DATE_ADD('2018-10-31 23:59:59',INTERVAL 1 SECOND) AS C1,
    -> DATE_ADD('2018-10-31 23:59:59',INTERVAL '1:1' MINUTE_SECOND) AS C2,
    -> ADDDATE('2018-10-31 23:59:59',INTERVAL 1 SECOND) AS C3;
+---------------------+---------------------+---------------------+
| C1                  | C2                  | C3                  |
+---------------------+---------------------+---------------------+
| 2018-11-01 00:00:00 | 2018-11-01 00:01:00 | 2018-11-01 00:00:00 |
+---------------------+---------------------+---------------------+
1 row in set (0.00 sec)
```

由执行结果可以看出，DATE_ADD(date,INTERVAL expr type) 和 ADDDATE(date,INTERVAL expr type) 函数的功能完全相同，在原始时间 '2018-10-31 23:59:59' 上加 1 秒之后结果都是 '2018-11-01 00:00:00' ；在原始时间上加 1 分钟 1 秒的写法是表达式 '1:1'，最终可得结果 '2018-11-01 00:01:00'。

【实例 2】使用 ADDDATE() 函数执行日期的加操作，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT ADDDATE('2017-11-30 23:59:59', INTERVAL 1 SECOND) AS col1,
    -> ADDDATE('2017-11-30 23:59:59' ,INTERVAL '1:1' MINUTE_SECOND) AS col2;
+---------------------+---------------------+
| col1                | col2                |
+---------------------+---------------------+
| 2017-12-01 00:00:00 | 2017-12-01 00:01:00 |
+---------------------+---------------------+
1 row in set (0.02 sec)
```

由运行结果可以看到，ADDDATE('2017-11-30 23：59：59'，INTERVAL 1 SECOND) 函数执行的结果将时间增加 1 秒后返回，结果为“2017-12-01 00：00：00”；ADDDATE('2017-11-30 23：59：59'，INTERVAL'1：1'MINUTE_SECOND) 函数的日期运算类型是 MINUTE_SECOND，将指定时间增加 1 分 1 秒后返回，结果为“2017-12-01 00：01：00”。