# PHP substr()：截取字符串

> 原文：[`c.biancheng.net/view/6142.html`](http://c.biancheng.net/view/6142.html)

在对字符串进行处理时，有时我们需要对字符串进行截取操作。在 PHP 中，截取字符串可以通过 PHP 的预定义函数 substr() 来实现，本节我们就来介绍一下 substr() 函数的使用。

substr() 函数可以从字符串的指定位置截取一定长度的字符，这段被截取的字符可称为“子串”或者“子字符串”，其语法格式如下：

substr($string, $start [, $length])

参数说明如下：

*   $string：需要截取的字符串，该字符串至少含有一个字符；
*   $start：截取字符串的起始位置；
    *   如果 $start 是非负数，那么字符串将从 $string 的第 $start 个字符处开始截取，$start 从 0 开始计算。例如在字符串“abcdef”中，在 0 位置的字符是“a”，在 2 位置的字符串是 “c” 等等；
    *   如果 $start 是负数，那么字符串将从 $string 结尾处向前数第 $start 个字符开始，$start 从 -1 开始计算。例如在字符串“abcdef”中，在 -1 位置的字符是“f”，在 -3 位置的字符是“d”等等；
    *   如果 $string 的长度小于 $start，将返回 FALSE。
*   $length：可选参数，表示截取字符串的长度。
    *   如果 $length 为正数，那么字符串将从 $start 位置向后截取最多 $length 个字符；
    *   如果 $length 为负数，那么 $string 末尾的 $length 个字符将会被省略（若 $start 是负数则从字符串尾部算起）；
    *   如果 $length 的值为 0，FALSE 或 NULL，那么将返回一个空字符串；
    *   如果没有提供 $length，那么返回的子字符串将从 $start 位置开始直到字符串的结尾。

【示例】使用 substr() 函数，截取指定长度的字符串。

```

<?php
    $str = 'http://c.biancheng.net/php/';
    echo substr($str, 7).'<br>';
    echo substr($str, -5).'<br>';
    echo substr($str, 7, 15).'<br>';
    echo substr($str, 7, -5).'<br>';
    echo substr($str, -20, -5).'<br>';
    echo substr($str, -20, 15).'<br>';
    var_dump(substr($str, 30));
    echo '<br>';
    var_dump(substr($str, 7, 0));
?>
```

运行结果如下：

c.biancheng.net/php/
/php/
c.biancheng.net
c.biancheng.net
c.biancheng.net
c.biancheng.net
bool(false)
string(0) ""

substr() 函数对英文的处理是没有任何问题的，但是在开发中我们往往是处理中文字符比较多，substr() 函数虽然也能处理中文，但是不是很完美，下一节我们将为大家介绍 PHP 中如何截取中文字符。