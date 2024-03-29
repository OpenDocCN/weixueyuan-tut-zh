# MySQL DATE_FORMAT 函数：格式化指定的日期

> 原文：[`c.biancheng.net/mysql/date_format.html`](http://c.biancheng.net/mysql/date_format.html)

MySQL 中 DATE_FORMAT(date，format) 函数是根据 format 指定的格式显示 date 值。

DATE_FORMAT() 函数接受两个参数：

*   date：是要格式化的有效日期值
*   format：是由预定义的说明符组成的格式字符串，每个说明符前面都有一个百分比字符(%)。

主要的 format 格式如下表所示。

| 说明符 | 说明 |
| %a | 工作日的缩写名称（Sun~Sat) |
| %b | 月份的缩写名称（Jan…Dec) |
| %c | 月份，数字形式（0~12) |
| %D | 带有英语后缀的该月日期(0th, 2st, 3nd,…） |
| %d | 该月日期，数字形式（00~31) |
| %e | 该月日期，数字形式（(0~31) |
| %f | 微秒（000000 …999999) |
| %H | 以 2 位数表示 24 小时（00~23) |
| %h, %I | 以 2 位数表示 12 小时（01~12) |
| %i | 分钟，数字形式(00~59) |
| %j | —年中的天数（001~366) |
| %k | 以 24 小时（0~23)表示 |
| %l | 以 12 小时（1~12)表示 |
| %M | 月份名称（January~December) |
| %m | 月份，数字形式（00~12) |
| %p | 上午（AM) 或下午（PM) |
| %r | 时间，12 小时制（小时 (hh): 分钟 (mm) : 秒数 (ss) 后加 AM 或 PM) |
| %S, %s | 以 2 位数形式表示秒（00~59) |
| %T | 时间，24 小时制（小时 (hh): 分钟 (mm): 秒数 (ss)) |
| %U | 周（00~53)，其中周日为每周的第一天 |
| %u | 周（00~53)，其中周一为每周的第一天 |
| %V | 周（01~53)，其中周日为每周的第一天，和％X 同时使用 |
| %v | 周（01~53)，其中周一为每周的第一天，和%x 同时使用 |
| %W | 星期标识（周日、周一、周二…周六） |
| %w | —周中的每日（0= 周日…6= 周六） |
| %X | 该周的年份，其中周日为每周的第一天，数字形式，4 位数，和％V 同时使用 |
| %x | 该周的年份，其中周一为每周的第一天，数字形式，4 位数，和%v 同时使用 |
| %Y | 4 位数形式表示年份 |
| %y | 2 位数形式表示年份 |
| %% | %一个文字字符 |

【实例】使用 DATE_FORMAT() 函数格式化输出日期和时间值，输入的 SQL 语句和执行结果如下所示。

```

mysql> SELECT DATE_FORMAT('2017-11-15 21:45:00','%W %M %D %Y') AS col1,
    -> DATE_FORMAT('2017-11-15 21:45:00','%h:i% %p %M %D %Y') AS col2;
+------------------------------+----------------------------+
| col1                         | col2                       |
+------------------------------+----------------------------+
| Wednesday November 15th 2017 | 09:i PM November 15th 2017 |
+------------------------------+----------------------------+
1 row in set (0.03 sec)
```

由运行结果可以看到，“2017-11-15 21：45：00”分别按照不同的参数已转换为不同格式的日期值和时间值。