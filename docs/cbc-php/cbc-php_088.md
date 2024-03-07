# PHP 计算时间差

> 原文：[`c.biancheng.net/view/6209.html`](http://c.biancheng.net/view/6209.html)

假如想知道用户最后登录网站距离现在已经过去了多长时间，这时就要计算两个日期之间的时间差。

在 PHP 中，计算两个日期之间的时间差需要先把两个日期转换成纪元时间戳再计算，示例如下：

```

<?php
//2019 年 1 月 1 日 19 点 30 分 0 秒
$start = mktime(19,30,0,1,1,2019);
//2019 年 7 月 7 日 7 点 30 分 0 秒
$end = mktime(7,30,0,7,7,2019);
$diff_seconds = $end - $start;
//一周的秒数是 24*60*60＝604800 秒
$diff_weeks = floor($diff_seconds/604800);
//一天的描述是 24*60*60=86400
$diff_days = floor($diff_seconds/86400);
$diff_hours = floor($diff_seconds/3600);
$diff_minutes = floor($diff_seconds/60);
echo "2019-1-1 19:30:0 和 2019-7-7 7:30:0 之间相差：<br />".
    "$diff_seconds 秒<br />".
    "$diff_weeks 个星期<br />".
    "$diff_days 天<br />".
    "$diff_hours 个小时<br />".
    "$diff_minutes 分钟<br />";
?>
```

执行以上程序的输出结果为：

2019-1-1 19:30:0 和 2019-7-7 7:30:0 之间相差：
16113600 秒
26 个星期
186 天
4476 个小时
268560 分钟