# PHP preg_split()：使用正则表达式分割字符串

> 原文：[`c.biancheng.net/view/6251.html`](http://c.biancheng.net/view/6251.html)

PHP preg_split() 函数通过一个正则表达式来分割字符串，语法如下：

array preg_split ( string $pattern , string $subject [, int $limit = -1 [, int $flags = 0 ]] )

参数说明如下：

*   pattern：用于匹配的模式，也即正则表达式。
*   subject　要分隔的字符串。
*   limit：可选参数，如果指定，就将限制分隔得到的子串最多只有 limit 个，并且最后一个子串将包含所有剩余部分。limit 值为 -1、0 或 NULL 时都代表“不限制”，建议使用 NULL。
*   flags：可选参数，它有 3 个取值。
    *   若设置为 PREG_SPLIT_NO_EMPTY，则 preg_split() 将返回分隔后的非空部分。
    *   若设置为 PREG_SPLIT_DELIM_CAPTURE，则分隔的模式中的括号表达式将被捕获并返回。
    *   若设置为 PREG_SPLIT_OFFSET_CAPTURE，则对于每一个出现的匹配返回时会附加字符串偏移量。

    注意：这将会改变返回数组中的每一个元素，使每个元素成为一个由第 0 个元素为分隔后的子串、第 1 个元素为该子串在 subject 中的偏移量组成的数组。

返回值：返回一个使用 pattern 分割 subject 字符串后得到的子串组成的数组。

该函数的使用示例如下：

```

<?php
    echo "<pre>";
    $subject = 'PHP 教程：http://c.biancheng.net/php/, Python 教程：http://c.biancheng.net/python/';
    $pattern = '/[\s,：]+/';
    print_r( preg_split($pattern, $subject) );
    print_r( preg_split($pattern, $subject, 3) );
?>
```

执行以上程序的结果如下：

Array
(
    [0] => PHP 教程
    [1] => http://c.biancheng.net/php/
    [2] => Python 教程
    [3] => http://c.biancheng.net/python/
)
Array
(
    [0] => PHP 教程
    [1] => http://c.biancheng.net/php/
    [2] => Python 教程：http://c.biancheng.net/python/
)