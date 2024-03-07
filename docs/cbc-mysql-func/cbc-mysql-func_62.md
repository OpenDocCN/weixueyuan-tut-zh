# MySQL CASE 函数：搜索语句

> 原文：[`c.biancheng.net/mysql/case.html`](http://c.biancheng.net/mysql/case.html)

除了 IF 函数，MySQL 还提供了一个替代的条件语句 CASE。 MySQL CASE 语句使代码更加可读和高效。

CASE 语句有两种形式：简单的和可搜索 CASE 语句。

## 简单的 CASE 语句

简单的 CASE 语句就是指使用简单 CASE 语句来检查表达式的值与一组唯一值的匹配。

简单的 CASE 语句的语法：

```

CASE  <表达式>
   WHEN <值 1> THEN <操作>
   WHEN <值 2> THEN <操作>
   ...
   ELSE <操作>
END CASE;
```

其中：<表达式> 可以是任何有效的表达式。我们将 <表达式> 的值与每个 WHEN 子句中的值进行比较，例如 <值 1>，<值 2> 等。如果 <表达式> 和 <值 n> 的值相等，则执行相应的 WHEN 分支中的命令 <操作>。如果 WHEN 子句中的 <值 n> 没有与 <表达式> 的值匹配，则 ELSE 子句中的命令将被执行。ELSE 子句是可选的。 如果省略 ELSE 子句，并且找不到匹配项，MySQL 将引发错误。
【实例 1】使用 CASE 函数根据表达式的取值返回相应值，输入 SQL 语句和执行结果如下

```

mysql> SELECT CASE WEEKDAY(NOW()) WHEN 0 THEN '星期一' WHEN 1 THEN '星期二' WHEN
2 THEN '星期三' WHEN 3 THEN '星期四' WHEN 4 THEN '星期五' WHEN 5 THEN '星期六'
ELSE '星期天' END AS COLUMN1,NOW(),WEEKDAY(NOW()),DAYNAME(NOW());
+---------+---------------------+----------------+----------------+
| COLUMN1 | NOW()               | WEEKDAY(NOW()) | DAYNAME(NOW()) |
+---------+---------------------+----------------+----------------+
| 星期四  | 2019-02-28 13:45:43 |              3 | Thursday       |
+---------+---------------------+----------------+----------------+
1 row in set, 7 warnings (0.00 sec)
```

由执行结果可以看出，NOW() 函数得到当前系统时间是 2019 年 2 月 28 日，DAYNAME(NOW()) 得到当天是 'Thursday '，WEEKDAY(NOW()) 函数返回当前时间的工作日索引是 3，即对应的是星期四。

## 可搜索的 CASE 语句

简单 CASE 语句仅允许将表达式的值与一组不同的值进行匹配。 为了执行更复杂的匹配，如范围，则可以使用可搜索 CASE 语句。 可搜索 CASE 语句等同于 IF 语句，但是它的构造更加可读。
可搜索 CASE 语句的语法：

```

CASE
    WHEN <条件 1> THEN <命令>
    WHEN <条件 2> THEN <命令>
    ...
    ELSE commands
END CASE;
```

MySQL 分别计算 WHEN 子句中的每个条件，直到找到一个值为 TRUE 的条件，然后执行 THEN 子句中的相应 <命令>。如果没有一个条件为 TRUE，则执行 ELSE 子句中的 <命令>。如果不指定 ELSE 子句，并且没有一个条件为 TRUE，MySQL 将发出错误消息。MySQL 不允许在 THEN 或 ELSE 子句中使用空的命令。 如果您不想处理 ELSE 子句中的逻辑，同时又要防止 MySQL 引发错误，则可以在 ELSE 子句中放置一个空的 BEGIN END 块。

【实例 2】使用 CASE 函数根据表达式的取值返回相应值，输入 SQL 语句和执行结果如下

```

mysql> SELECT CASE WHEN WEEKDAY(NOW())=0 THEN '星期一' WHEN WEEKDAY(NOW())=1 THE
N '星期二'  WHEN WEEKDAY(NOW())=2 THEN '星期三' WHEN WEEKDAY(NOW())=3 THEN '星期
四' WHEN WEEKDAY(NOW())=4 THEN '星期五' WHEN WEEKDAY(NOW())=5 THEN '星期六' WHEN
WEEKDAY(NOW())=6 THEN '星期天' END AS COLUMN1,NOW(),WEEKDAY(NOW()),DAYNAME(NOW(
));
+---------+---------------------+----------------+----------------+
| COLUMN1 | NOW()               | WEEKDAY(NOW()) | DAYNAME(NOW()) |
+---------+---------------------+----------------+----------------+
| 星期四  | 2019-02-28 14:08:00 |              3 | Thursday       |
+---------+---------------------+----------------+----------------+
1 row in set, 7 warnings (0.00 sec)
```

此例跟上例的返回结果一样，只是使用了 CASE 函数的不同写法，WHEN 后面为表达式，当表达式的返回结果为 TRUE 时取 THEN 后面的值，如果都不是，则返回 ELSE 后面的值。