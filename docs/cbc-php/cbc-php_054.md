# PHP 字符串拼接（连接）

> 原文：[`c.biancheng.net/view/6138.html`](http://c.biancheng.net/view/6138.html)

PHP 中可以使用字符串连接符`.`来拼接字符串，它可以把两个或两个以上的字符串拼接成一个新的字符串。

字符串拼接有两种形式，分别是直接使用字符串连接符`.`和赋值运算符`.=`。具体语法格式如下：

$string = string1.string2.string3\. ······ .stringn;
或者
$string = string1;
$string .= string2;
$string .= string3;
······
$string .= stringn;

【示例】使用字符串连接符`.`拼接字符串。

```

<?php
    $a = 'C 语言中文网';
    $b = 'http://c.biancheng.net/<br>';
    $c = '一个在线学习编程的网站';

    $str = '欢迎访问“'.$a.'”<br>'.'网址：'.$b.$c;
    echo $str;
?>
```

运行结果如下：

欢迎访问“C 语言中文网”
网址：http://c.biancheng.net/
一个在线学习编程的网站

【示例】使用赋值运算符`.=`连接字符串。

```

<?php
    $str = '欢迎访问“C 语言中文网”<br>';
    $str .= '网址：http://c.biancheng.net/<br>';
    $str .= '一个在线学习编程的网站';

    echo $str;
?>
```

运行结果如下：

欢迎访问“C 语言中文网”
网址：http://c.biancheng.net/
一个在线学习编程的网站