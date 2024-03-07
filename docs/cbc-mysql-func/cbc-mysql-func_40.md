# MySQL WEEK 函数：获取指定日期是一年中的第几周

> 原文：[`c.biancheng.net/mysql/week.html`](http://c.biancheng.net/mysql/week.html)

MySQL WEEK() 函数计算日期 date 是一年中的第几周。WEEK(date,mode) 函数允许指定星期是否起始于周日或周一，以及返回值的范围是否为 0～52 或 1～53。

WEEK 函数接受两个参数：

*   date 是要获取周数的日期。
*   mode 是一个可选参数，用于确定周数计算的逻辑。

如果忽略 mode 参数，默认情况下 WEEK 函数将使用 default_week_format 系统变量的值。要获取 default_week_format 变量的当前值，请使用 SHOW VARIABLES 语句如下：

```

mysql> SHOW VARIABLES LIKE 'default_week_format';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| default_week_format | 0     |
+---------------------+-------+
1 row in set
```

【实例】使用 WEEK(date) 函数查询指定日期是一年中的第几周。

```

mysql> SELECT WEEK('2018-10-25',1);
+----------------------+
| WEEK('2018-10-25',1) |
+----------------------+
|                   43 |
+----------------------+
1 row in set (0.00 sec)
```

由运行结果可知，2018-10-25 是一年中的第 43 周。