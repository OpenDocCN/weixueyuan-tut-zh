# PHP 设置时区

> 原文：[`c.biancheng.net/view/6199.html`](http://c.biancheng.net/view/6199.html)

时间是生活中必不可少的，在 PHP 中也是一样，时间有时候要存储起来记录某件事情或者在浏览器中展示出来。

在 PHP 中是通过日期和时间函数来获取日期和时间的。日期和时间函数依赖于服务器的时间设置，服务器的时间设置默认是格林尼治时间（零时区时间），如果不特意设置时间为特定时区时间，那么通过 PHP 有关函数获取到的时间为零时区的时间，比北京时间少 8 个小时。

## 时区的划分

全球分为 24 个时区，每个时区都有自己的本地时间，同一时间内各时区的本地时间相差 1~23 小时，如英国伦敦本地时间与北京本地时间相差 8 个小时。

在国际无线电通信领域，使用一个统一的时间，称为通用协调时间（Universal Time Coordinated，UTC），UTC 与格林威治标准时间（Greenwich Mean Time，GMT）相同。

## PHP 中的时区设置

由于 PHP5.0 对 data() 函数进行了重写，所以目前的日期时间函数比系统时间少 8 个小时。PHP 语言默认设置的是标准的格林威治时间（即采用的是零时区），所以要获取本地当前的时间必须更改 PHP 语言中的时区设置。

在 PHP 中设置时区的方法有三种，下面来分别介绍一下。

#### 1) 在配置文件中设置

PHP 的配置文件就是在 PHP 安装目录下的 php.ini 文件，找到并打开它。然后在文件中搜索`date.timezone`，我们会看到如下所示的一段信息：

[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
;date.timezone =

其中`;date.timezone =`前面的`;`与 PHP 中的`//`功能相同，都是注释的意思，这里我们需要将这个`;`去掉，并在这一句的`=`后面填写对应的时区标识符，如下所示：

[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
date.timezone = Asia/Shangha

时区标识符有很多，可以通过访问“[`www.php.net/manual/zh/timezones.php`](https://www.php.net/manual/zh/timezones.php)”来查看，下面列举了一些常用的时区标识符及其含义：

*   Asia/Shanghai —— 上海
*   Asia/Chongqing —— 重庆
*   Asia/Urumqi —— 乌鲁木齐
*   Asia/Hong_Kong —— 香港
*   Asia/Macao —— 澳门
*   Asia/Taipei —— 台北
*   Asia/Singapore —— 新加坡
*   PRC —— 中国时区

提示：在国内的话我们将时区设置为“Asia/Shanghai 或者 PRC”都可以，设置成功之后记得重启一下服务器！

【示例】下面通过一段代码来验证一下我们的设置是否生效。

```

<?php
    echo date('Y-m-d H:i:s',time());
?>
```

运行结果如下：

2020-03-26 09:56:52

如果运行结果与我们电脑的本地时间相等的话，就说明我们设置成功了。

#### 2) 使用 ini_set() 函数设置时区

PHP 中的 ini_set() 函数可以设置指定配置选项的值，这个配置选项会在脚本运行时保持新的值，并在脚本结束时恢复。函数的语法格式如下：

ini_set($varname, $newvalue)

其中 $varname 为待修改的配置选项，$newvalue 为配置选项的新值。

提示：ini_set() 函数并不能修改所有的配置选项，可以通过访问“[`www.php.net/manual/zh/ini.list.php`](https://www.php.net/manual/zh/ini.list.php)”来查看可以修改的配置选项。

【示例】使用 ini_set() 函数设置时区。

```

<?php
    ini_set('date.timezone', 'GMT');
    echo '当前的格林尼治时间为：'.date('Y-m-d H:i:s',time()).'<br>';
    ini_set('date.timezone', 'Asia/Urumqi');
    echo '乌鲁木齐的当前时间为：'.date('Y-m-d H:i:s',time()).'<br>';
    ini_set('date.timezone', 'Asia/Shanghai');
    echo '上海的当前时间为：'.date('Y-m-d H:i:s',time());
?>
```

运行结果如下：

当前的格林尼治时间为：2020-03-26 02:37:31
乌鲁木齐的当前时间为：2020-03-26 08:37:31
上海的当前时间为：2020-03-26 10:37:31

#### 3) 使用 date_default_timezone_set() 函数设置时区

PHP 中的 date_default_timezone_set() 函数可以为脚本中的所有时间日期函数设置一个默认时区，其语法格式如下：

date_default_timezone_set($timezone_identifier)

参数 $timezone_identifier 为时区标识符，例如 UTC（格林尼治时间）或 Europe/Lisbon（葡萄牙）。

自 PHP5.1.0 起（此版本日期时间函数被重写了），如果时区不合法则每个对日期时间函数的调用都会产生一条 E_NOTICE 级别的错误信息，如果使用系统设定或 TZ 环境变量则还会产生 E_STRICT 级别的信息。

【示例】使用 date_default_timezone_set() 函数设置时区。

```

<?php
    date_default_timezone_set('Asia/Urumqi');
    echo '乌鲁木齐的当前时间为：'.date('Y-m-d H:i:s',time()).'<br>';
    date_default_timezone_set('Europe/Lisbon');
    echo '葡萄牙的当前时间为：'.date('Y-m-d H:i:s',time()).'<br>';
    date_default_timezone_set('Asia/Shanghai');
    echo '上海的当前时间为：'.date('Y-m-d H:i:s',time());
?>
```

运行结果如下：

乌鲁木齐的当前时间为：2020-03-26 08:35:59
葡萄牙的当前时间为：2020-03-26 02:35:59
上海的当前时间为：2020-03-26 10:35:59