# PHP trim()：去除字符串两边的空格

> 原文：[`c.biancheng.net/view/6143.html`](http://c.biancheng.net/view/6143.html)

在网站中，用户在输入数据时，经常会在无意中输入多余的空格或者其它特殊字符。有些情况下，字符串中不允许出现空格和特殊字符，此时就需要去除字符串中的空格和特殊字符。

在 PHP 中提供了三个函数来去除字符串左右两边的空白字符和特殊字符，如下所示：

*   trim() 函数：去除字符串左右两边的空白字符和特殊字符；
*   ltrim() 函数：去除字符串左边的空白字符和特殊字符；
*   rtrim() 函数：去除字符串右边的空白字符和特殊字符。

#### 1) trim() 函数

trim() 函数可以去除字符串首尾的空白字符（或者其他字符）。语法格式如下：

trim($str [, $character_mask = " \t\n\r\0\x0B"])

参数说明如下：

*   $str：待处理的字符串；
*   $character_mask：可选参数，用于指定所有要去除的字符，也可以使用“..”列出一个字符范围。

如果不指定 $character_mask 参数，trim() 函数将去除下面这些字符：

*   " "：普通空格符；
*   "\t"：制表符；
*   "\n"：换行符；
*   "\r"：回车符；
*   "\0"：空字节符；
*   "\x0B"：垂直制表符。

【示例】使用 trim() 函数去除字符串左右两边的空格或特殊字符。

```

<?php
    $str = " C 语言中文网 \n";
    var_dump(trim($str));
    echo '<br>';
    $str = '@_@ http://c.biancheng.net/php/@ @_';
    var_dump(trim($str,'@ _'));
    echo '<br>';
    $str = "123PHP 教程 456";
    var_dump(trim($str,'1..6'));
?>
```

运行结果如下：

string(16) "C 语言中文网"
string(33) "http://c.biancheng.net/php/"
string(10) "PHP 教程"

#### 2) ltrim() 函数

ltrim() 函数可以去除字符串开头的空白字符或者其它指定的字符，语法格式如下：

ltrim($str [, $character_mask])

ltrim() 函数的参数与 trim() 函数的参数及功能相同，不同的是，ltrim() 函数只会处理字符串开头的字符。

【示例】使用 ltrim() 函数去除字符串开头的空白字符或特殊字符。

```

<?php
    $str = " C 语言中文网 \n";
    var_dump(ltrim($str));
    echo '<br>';
    $str = '@_@ http://c.biancheng.net/php/@ @_';
    var_dump(ltrim($str,'@ _'));
    echo '<br>';
    $str = "123PHP 教程 456";
    var_dump(ltrim($str,'1..6'));
?>
```

运行结果如下：

string(18) "C 语言中文网 "
string(37) "http://c.biancheng.net/php/@ @_"
string(13) "PHP 教程 456"

#### 3) rtrim() 函数

rtrim() 函数可以去除字符串末尾的空白字符或者其它指定的字符，语法格式如下：

rtrim($str [, $character_mask])

rtrim() 函数的参数与 trim()、ltrim() 函数的参数与功能相同，只是 rtrim() 函数只会处理字符串末尾的字符。

【示例】使用 rtrim() 函数去除字符串开头的空白字符或特殊字符。

```

<?php
    $str = " C 语言中文网 \n";
    var_dump(rtrim($str));
    echo '<br>';
    $str = '@_@ http://c.biancheng.net/php/@ @_';
    var_dump(rtrim($str,'@ _'));
    echo '<br>';
    $str = "123PHP 教程 456";
    var_dump(rtrim($str,'1..6'));
?>
```

运行结果如下：

string(17) " C 语言中文网"
string(37) "@_@ http://c.biancheng.net/php/"
string(13) "123PHP 教程"