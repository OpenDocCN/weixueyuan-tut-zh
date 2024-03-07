# PHP 获取当前时间（5 种方式）

> 原文：[`c.biancheng.net/view/6206.html`](http://c.biancheng.net/view/6206.html)

在日期和时间函数中，UNIX 时间戳的获取非常重要，时间戳是一个字符序列，是指格林尼治时间 1970 年 01 月 01 日 00 时 00 分 00 秒（北京时间 1970 年 01 月 01 日 08 时 00 分 00 秒）起至现在的总毫秒数。下面介绍几个获取当前时间的函数。

## gmmktime()

gmmktime() 函数可取得 GMT 日期的 UNIX 时间戳。语法如下：

int gmmktime ([ int $hour [, int $minute [, int $second [, int $month [, int $day [, int $year [, int $is_dst ]]]]]]] )

该函数的参数可以从右到左依次空着，空着的参数会被设为相应的当前 GMT 值。

使用示例如下：

```

<?php
echo gmmktime();  //没有设置参数, 则默认取得当前 GMT 时间
echo gmmktime(0,45,3,7,7,2016);  //设置参数表示 GMT 时间 2016 年 7 月 7 日 0 点 45 分 3 秒
?>
```

执行以上程序的打印结果为：

1467909956   1467852303

## mktime()

mktime() 也可取得一个日期的 UNIX 时间戳。语法如下：

int mktime ([ int $hour = date("H") [, int $minute = date("i") [, int $second = date("s") [, int $month = date("n") [, int $day = date("j") [, int $year = date("Y") [, int $is_dst = -1 ]]]]]]] )

该函数根据给出的参数返回 UNIX 时间戳。时间戳是一个长整数，包含了从 UNIX 纪元到给定时间的秒数。

和 gmmktime() 函数一样，该函数的参数也可以从右向左省略，任何省略的参数会被设置成本地日期和时间的当前值。

使用示例如下：

```

<?php
echo mktime();  // 没有设置参数则默认取得当前 GMT 时间
echo mktime(0,45,3,7,7,2016);  //设置参数表示 GMT 时间 2016 年 7 月 7 日 0 点 45 分 3 秒
?>
```

执行以上程序的打印结果为：

1467910465 1467852303

## microtime()

microtime() 可获得当前 UNIX 时间戳和微秒数。语法如下：

mixed microtime ([ bool $get_as_float ] )

如果设置 get_as_float 参数值为 true，microtime() 将返回一个浮点数；若不带参数，则返回一个“msec sec”格式的字符串，其中 sec 是自 UNIX 纪元起到现在的秒数，msec 是微秒部分。字符串的两部分都是以秒为单位返回的。

使用示例如下：

```

<?php
echo microtime();  //返回 msec sec 格式字符串表示时间
echo "<br/>";
echo microtime(true);  //返回一个浮点型字符串表示时间
?>
```

执行以上程序的打印结果如下：

0.40474900 1467910862
1467910862.4048

## time()

time() 函数可返回当前的 UNIX 时间戳。语法如下：

int time ( void )

time() 函数的语法比较简单，使用示例如下：

```

<?php
echo time();
?>
```

执行以上程序的打印结果为：

1467911104

## getdate()

getdate() 可取得日期时间信息。语法如下：

array getdate ([ int $timestamp = time() ] )

该函数返回一个根据 timestamp 得出的包含有日期信息的关联数组 array。如果没有给出时间戳，则认为是当前本地时间（此时和 time() 函数取值相同）。

其返回的关联数组中的键名单元如表所示。

getdate() 函数返回关联数组键名

| 键名 | 说明 | 返回值例子 |
| seconds | 秒的数字表示 | 0 到 59 |
| minutes | 分钟的数字表示 | 0 到 59 |
| hours | 小时的数字表示 | 0 到 23 |
| mday | 月份中第几天的数字表示 | 1 到 31 |
| wday | 星期中第几天的数字表示 | 0（周日）到 6（周六） |
| mon | 月份的数字表示 | 1 到 12 |
| year | 4 位数字表示的完整年份 | 比如：1999 或 2019 |
| yday | 一年中第几天的数字表示 | 0 到 365 |
| weekday | 星期几的完整文本表示 | Monday 到 Sunday |
| month | 月份的完整文本表示，，比如 January 或 April | January 到 December |
| 0 | 自从 UNIX 纪元开始至今的秒数，和 time() 的返回值以及用于 date() 的值类似 | 系统相关，典型值为 -2147483648 ~ 2147483647 |

getdate() 函数的使用示例如下：

```

<?php
echo "<pre>";
var_dump(getdate());
?>
```

执行以上程序的打印结果如下：

array(11) {
    ["seconds"]=>int(57)
    ["minutes"]=>int(18)
    ["hours"]=>int(17)
    ["mday"]=>int(7)
    ["wday"]=>int(4)
    ["mon"]=>int(7)
    ["year"]=>int(2016)
    ["yday"]=>int(188)
    ["weekday"]=>string(8) "Thursday"
    ["month"]=>string(4) "July"
    [0]=>int(1467911937)
}