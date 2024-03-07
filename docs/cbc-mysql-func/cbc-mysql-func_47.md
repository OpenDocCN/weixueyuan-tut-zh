# MySQL DATE_SUB 和 SUBDATE 函数：日期减法运算

> 原文：[`c.biancheng.net/mysql/date_sub_subdate.html`](http://c.biancheng.net/mysql/date_sub_subdate.html)

MySQL DATE_SUB(date,INTERVAL expr type) 和 SUBDATE(date,INTERVAL expr type) 两个函数作用相同，都是执行日期的减法运算。

DATE_SUB() 和 SUBDATE() 函数接受两个参数：

*   date 是 DATE 或 DATETIME 的起始值。
*   expr 是一个字符串，用于确定从起始日期减去的间隔值。type 是 expr 可解析的间隔单位，例如 DAY，HOUR 等

【实例】使用 DATE_SUB(date,INTERVAL expr type) 和 SUBDATE(date,INTERVAL expr type) 函数执行日期的减运算，输入 SQL 语句与执行结果如下。

```

mysql> SELECT DATE_SUB('2018-01-02',INTERVAL 31 DAY) AS C1,
    -> SUBDATE('2018-01-02',INTERVAL 31 DAY) AS C2,
    -> DATE_SUB('2018-01-01 00:01:00',INTERVAL '0 0:1:1' DAY_SECOND) AS C3;
+------------+------------+---------------------+
| C1         | C2         | C3                  |
+------------+------------+---------------------+
| 2017-12-02 | 2017-12-02 | 2017-12-31 23:59:59 |
+------------+------------+---------------------+
1 row in set (0.00 sec)
```

由执行结果可以看出，DATE_SUB(date,INTERVAL expr type) 和 SUBDATE (date,INTERVAL expr type) 函数的功能完全相同。

> 提示：DATE_ADD(date,INTERVAL expr type) 和 DATE_SUB(date,INTERVAL expr type) 函数在指定加减的时间段时也可以指定负值，加法的负值即返回原始时间之前的日期和时间，减法的负值即返回原始时间之后的日期和时间。