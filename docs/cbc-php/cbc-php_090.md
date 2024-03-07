# PHP checkdate()：检验日期和时间是否有效

> 原文：[`c.biancheng.net/view/6211.html`](http://c.biancheng.net/view/6211.html)

一年有 12 个月，一周有 7 天，一个月有 30 天、31 天或者 29 天等，这些对人类来说是基本的常识，但是计算机并不能分辨数据的对与错，比如如何防止用户输入一个类似 2016 年 7 月 32 日这样的一个无效日期。

PHP 中提供了 checkdate() 函数来检验日期和时间的有效性，语法如下：

bool checkdate ( int $month , int $day , int $year )

month 的值是从 1 到 13，day 的值在给定的 month 所应该具有的天数范围之内，闰年也考虑进去，year 的值是从 1 到 32767。

如果给出的日期有效就返回 true，否则返回 false。

checkdate() 使用示例如下：

```

<?php
var_dump( checkdate(7, 32, 2019) );
var_dump( checkdate(7, 9, 2019) );
?>
```

执行以上程序的结果为：

bool(false) bool(true)