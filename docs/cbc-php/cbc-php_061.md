# PHP strlen()与 mb_strlen()：获取字符串长度

> 原文：[`c.biancheng.net/view/7412.html`](http://c.biancheng.net/view/7412.html)

要想处理一个字符串，首先需要知道该字符串的长度。在其他一些编程语言（如 JavaScript）中字符串的长度通常作为字符串的一个属性出现，而在 PHP 中，则需要通过相应的函数来获取。

在 PHP 中常用的计算字符串长度的函数有两个，分别是 strlen() 和 mb_strlen() 函数。当处理全英文字符串时，这两个函数的效果是一样的，而处理中英文混合或纯中文的字符串时，这两个函数会有一些差异，下面就来详细介绍一下。

## 1、strlen() 函数

strlen() 函数可以返回给定字符串的长度，其语法格式如下：

strlen($string)

参数 $string 为需要计算长度的字符串。如果 $string 为空则返回 0。

注意：在 strlen() 函数中，数字、英文、小数点、下划线和空格占一个字符长度；而一个 GB2312 编码的汉字占两个字符长度，一个 UTF-8 编码的汉字占三个字符长度。

【示例】使用 strlen() 函数获取字符串的长度。

```

<?php
    $str = "http://c.biancheng.net/php/";
    echo '字符串“'.$str.'”的长度为：'.strlen($str).'<br>';
    $str = "C 语言中文网";
    echo '字符串“'.$str.'”的长度为：'.strlen($str).'<br>';
    $str = "php";
    echo '字符串“'.$str.'”的长度为：'.strlen($str);
?>
```

运行结果如下：

字符串“http://c.biancheng.net/php/”的长度为：27
字符串“C 语言中文网”的长度为：16
字符串“php”的长度为：3

## 2、mb_strlen() 函数

mb_strlen() 函数同样可以返回字符串的长度，语法格式如下：

mb_strlen($str [, $encoding = mb_internal_encoding()])

其中，$str 为需要计算长度的字符串，$encoding 是可选参数，为字符编码，如果省略则使用内部字符编码。

mb_strlen() 函数的返回值为具有 $encoding 编码的字符串 $str 所包含的字符数，如果 $encoding 无效则返回 false。

注意：与 strlen() 函数不同，在 mb_strlen() 函数中，无论是汉字，还是英文、数字、小数点、下划线和空格，都只占一个字符长度。

【示例】 使用 mb_strlen() 函数获取字符串长度。

```

<?php
    $str = "C 语言中文网";
    echo '字符串“'.$str.'”的长度为：'.mb_strlen($str).'<br>';
    $str = "http://c.biancheng.net/php/";
    echo '字符串“'.$str.'”的长度为：'.mb_strlen($str).'<br>';
    $str = "PHP 教程";
    echo '字符串“'.$str.'”的长度为：'.mb_strlen($str);
?>
```

运行结果如下：

字符串“C 语言中文网”的长度为：6
字符串“http://c.biancheng.net/php/”的长度为：27
字符串“PHP 教程”的长度为：6

提示：mb_strlen 并不是 PHP 的核心函数，使用前需要确保在 php.ini 中加载了 php_mbstring.dll，即确保“extension=php_mbstring.dll”这一行存在并且没有被注释掉，否则会出现未定义函数的问题。