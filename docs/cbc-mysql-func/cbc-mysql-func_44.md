# MySQL TIME_TO_SEC 函数：将时间转换为秒值

> 原文：[`c.biancheng.net/mysql/time_to_sec.html`](http://c.biancheng.net/mysql/time_to_sec.html)

MySQL TIME_TO_SEC(time) 函数返回将参数 time 转换为秒数的时间值，转换公式为“小时 ×3600+ 分钟 ×60+ 秒”。

【实例】使用 TIME_TO_SEC(time) 函数将时间值转换为秒值。

```

mysql> SELECT TIME_TO_SEC('15:15:15');
+-------------------------+
| TIME_TO_SEC('15:15:15') |
+-------------------------+
|                   54915 |
+-------------------------+
1 row in set (0.00 sec)
```

由执行结果可以看出，根据计算公式“15×3600+15×60+25”得出结果秒数 54915。