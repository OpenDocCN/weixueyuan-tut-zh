# PHP mb_substr()：截取中文字符串

> 原文：[`c.biancheng.net/view/7398.html`](http://c.biancheng.net/view/7398.html)

在 Web 开发过程中，为了保持整个页面的美观，经常需要对一些超长的中文字符串进行缩略显示，而上一节介绍的《substr() 函数》对中文的处理不是很友好，那么本节就来为大家介绍一个类似的函数——mb_substr() 函数。

mb_substr() 函数可以从一个字符串中截取指定的一部分，与 substr() 函数不同的是，mb_substr() 函数不仅对英文字符有效，对中文字符同样有效，其语法格式如下：

mb_substr($str , $start [, $length = NULL [, $encoding = mb_internal_encoding()]])

参数说明如下：

*   $str：待截取的字符串，字符串中至少包含一个字符；
*   $start：截取字符串的起始位置；
    *   如果 $start 为非负数，那么字符串会从 $str 的第 $start 个字符的位置开始截取；
    *   如果 $start 是负数，那么字符串会从 $str 结尾处向前数第 $start 个字符的位置开始截取。
*   $length：可选参数，表示截取字符串的长度；
    *   如果 $length 为正数，那么字符串将从 $start 位置向后截取最多 $length 个字符；
    *   如果 $length 为负数，那么 $string 末尾的 $length 个字符将会被省略（若 $start 是负数则从字符串尾部算起）；
    *   如果 $length 的值 NULL 或者省略 $length，则会截取到字符串的末尾。
*   $encoding：可选参数，表示 $str 的字符编码，如果省略，则使用内部字符编码。

【示例】使用 mb_substr() 函数，截取指定长度的中文字符串。

```

<?php
    $str = '欢迎访问 C 语言中文网，一个在线学习编程的网站。';
    echo mb_substr($str, 4).'<br>';
    echo mb_substr($str, -19).'<br>';
    echo mb_substr($str, 4, 6).'<br>';
    echo mb_substr($str, 4, -16).'<br>';
    echo mb_substr($str, -19, -13).'<br>';
    echo mb_substr($str, -19, 6).'<br>';
    var_dump(mb_substr($str, 40));
    echo '<br>';
    var_dump(mb_substr($str, 4, null));
?>
```

运行结果如下：

C 语言中文网，一个在线学习编程的网站。
C 语言中文网，一个在线学习编程的网站。
C 语言中文网
C 语言
C 语言中文网
C 语言中文网
string(0) ""
string(55) "C 语言中文网，一个在线学习编程的网站。"

【示例】使用 mb_substr() 函数截取指定长度的字符串，超出的部分使用“...”代替。

```

<?php
    $str = 'C 语言中文网是一个在线学习编程的网站，我们发布了多套文字教程，它们都通俗易懂，深入浅出。';
    if(strlen($str)>18){
        echo mb_substr($str, 0, 18).'...';
    }else{
        echo $str;
    }
?>
```

运行结果如下：

C 语言中文网是一个在线学习编程的网站...