# MySQL DAYNAME 函数：获取指定日期的星期名称

> 原文：[`c.biancheng.net/mysql/dayname.html`](http://c.biancheng.net/mysql/dayname.html)

MySQL DAYNAME(date) 函数返回 date 对应的工作日英文名称，例如 Sunday、Monday 等。

【实例】使用 DAYNAME(date) 函数返回指定日期的工作日名称。

```

mysql> SELECT DAYNAME('2006-06-12');
+-----------------------+
| DAYNAME('2006-06-12') |
+-----------------------+
| Monday                |
+-----------------------+
1 row in set (0.00 sec)
```

由运行结果可知，2006-06-12 这一天是星期一，英文名称是 Monday。