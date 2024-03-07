# PHP implode()：数组转字符串

> 原文：[`c.biancheng.net/view/6156.html`](http://c.biancheng.net/view/6156.html)

通过前面的学习我们知道，可以使用《explode() 函数》将一个字符串转化为一个一维数组，在 PHP 中还有一个功能与之相反的函数——implode()。

implode() 函数可以将一个一维数组转化为字符串，其语法格式如下：

implode($glue, $array) 或者 implode($array)

其中，$glue 用来设置一个字符串，表示使用 $glue 将数组每个元素连接在一起，默认情况下 $glue 为空字符串；$array 为需要转换的数组。

提示：implode() 函数的 $glue 参数是可选的，可以省略。

【示例】使用 implode() 函数将数组转换为字符串。

```

<?php
    $arr = ['C 语言中文网','PHP 教程','http://c.biancheng.net/php/','implode()函数','数组转字符串'];
    $str = implode($arr);
    echo $str.'<br>';
    $str = implode(' ',$arr);
    echo $str.'<br>';
    $str = implode('，',$arr);
    echo $str.'<br>';
    $str = implode('--',$arr);
    echo $str.'<br>';
?>
```

运行结果如下：

C 语言中文网 PHP 教程 http://c.biancheng.net/php/implode()函数数组转字符串
C 语言中文网 PHP 教程 http://c.biancheng.net/php/ implode()函数 数组转字符串
C 语言中文网，PHP 教程，http://c.biancheng.net/php/，implode()函数，数组转字符串
C 语言中文网--PHP 教程--http://c.biancheng.net/php/--implode()函数--数组转字符串