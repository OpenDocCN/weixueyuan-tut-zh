# PHP explode()：字符串转数组

> 原文：[`c.biancheng.net/view/6148.html`](http://c.biancheng.net/view/6148.html)

在开发过程中，我们经常遇到需要将字符串转为数组的情况。PHP 内置了 explode() 函数，可以将字符串按特定的分隔符分割成若干个部分。

explode() 函数可以基于字符串分隔符拆分字符串，即它将一个字符串根据分隔符拆分为若干个子串，然后将这些子串组合成数组并返回。其语法格式如下：

explode($delimiter, $string [, $limit])

参数说明如下：

*   $delimiter：用于分割字符串的分隔字符；
*   $string：需要分割的字符串；
*   $limit：可选参数，可以为空，规定要返回数组元素的数目；
    *   如果 $limit 不为空且为正数，则返回的数组最多包含 $limit 个元素，最后的那个元素包含了 $string 的剩余部分；
    *   如果 $limit 不为空且为负数，则返回除了最后的 $limit 个元素外的所有元素；
    *   如果 $limit 为 0，则会被当作 1；
    *   如果 $limit 为空，则表示返回所有数组元素。

如果 $delimiter 为空字符串`""`，程序将会提示 Warning，并且 explode() 函数将返回 FALSE；如果 $delimiter 所包含的值在 $string 中找不到，并且使用了负数的 $limit，则会返回空的 array，否则返回包含 $string 单个元素的数组。

【示例】使用 explode() 函数分割一个字符串。

```

<?php
    $str = 'C 语言中文网,PHP 教程,http://c.biancheng.net/php/,explode()函数,字符串转数组';
    echo '<pre>';
    $arr = explode(',', $str);
    print_r($arr);
    $arr = explode(',', $str, 3);
    print_r($arr);
    $arr = explode(',', $str, -2);
    print_r($arr);
    $arr = explode(',', $str, 0);
    print_r($arr);
    $arr = explode('', $str);
    var_dump($arr);
    $arr = explode('@', $str, -1);
    print_r($arr);
?>
```

运行结果如下：

Array
(
    [0] => C 语言中文网
    [1] => PHP 教程
    [2] => http://c.biancheng.net/php/
    [3] => explode()函数
    [4] => 字符串转数组
)
Array
(
    [0] => C 语言中文网
    [1] => PHP 教程
    [2] => http://c.biancheng.net/php/,explode()函数,字符串转数组
)
Array
(
    [0] => C 语言中文网
    [1] => PHP 教程
    [2] => http://c.biancheng.net/php/
)
Array
(
    [0] => C 语言中文网,PHP 教程,http://c.biancheng.net/php/,explode()函数,字符串转数组
)

Warning:  explode(): Empty delimiter in D:\install\phpstudy\WWW\index.php on line 12

bool(false)
Array
(
)