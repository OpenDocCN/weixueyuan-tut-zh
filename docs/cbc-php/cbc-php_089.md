# PHP strtotime()：从字符串中提取时间日期

> 原文：[`c.biancheng.net/view/6210.html`](http://c.biancheng.net/view/6210.html)

PHP strtotime() 函数有两种用法：一种是将字符串形式的、用英文文本描述的日期时间解析为 UNIX 时间戳，一种是用来计算一些日期时间的间隔。

## 从字符串中解析日期时间

PHP strtotime() 函数可将英文文本描述的日期时间描述解析为 UNIX 时间戳，语法如下：

int strtotime ( string $time [, int $now = time() ] )

参数说明：

*   time 为字符串形式的日期时间；
*   now 规定用来计算返回值的时间戳。如果省略该参数，则使用当前时间。

返回值：执行成功则返回时间戳，否则返回 false。

strtotime() 函数的使用示例如下：

```

<?php
echo strtotime("now"), "<br />";
echo strtotime("10 September 2000"), "<br />";
echo strtotime("+1 day"), "<br />";
echo strtotime("+1 week"), "<br />";
echo strtotime("+1 week 2 days 4 hours 2 seconds"), "<br />";
echo strtotime("next Thursday"), "<br />";
echo strtotime("last Monday"), "<br />";
?>
```

执行以上程序打印出来的结果类似：

1572597225
968515200
1572683625
1573202025
1573389227
1573056000
1572192000

## 日期的加减运算

有时我们需要在一个日期上加减一定的时间间隔。可以使用 strtotime() 来计算一些日期时间间隔。示例如下：

```

<?php
$start = 'last Monday';
$interval = strtotime("$start + 4 days");
echo "现在\$interval 表示上周的" . date('l',$interval);
?>
```

执行以上程序的输出结果为：

现在 $interval 表示上周的 Friday。

如果日期使用时间戳表示，并且时间间隔也可用秒来表示，就可以从时间戳中减去时间间隔。示例如下；

```

<?php
$start = time();
echo date('Y-m-d',$start) , "<br />";
$interval = 7 * 24 * 3600;  //一周
$end = $start - $interval;
echo date('Y-m-d',$end);
?>
```

执行以上程序的输出结果为：

2019-10-31
2019-10-24

前后两个日期正好相差 7 天。