# PHP each()：返回数组当前元素的键值对

> 原文：[`c.biancheng.net/view/7432.html`](http://c.biancheng.net/view/7432.html)

PHP 中 each() 函数返回数组中当前元素的键值对，并将数组内部的指针指向数组的下一个元素。

each() 函数的语法格式如下：

each($array)

参数 $array 为待操作的函数。

注意：自 PHP7.2 起，each() 函数已经被弃用。

each() 函数需要传递一个数组作为参数，返回数组中当前元素的键/值对，并向下移动数组指针到下一个元素的位置。

函数返回值为带有四个元素的数组，键名分别为 0、1、key 和 value，其中键名 0 和 key 对应的值是一样的，是数组元素的键名，1 和 value 则是数组元素对应的值。

如果内部指针越过了数组的末端，则 each() 函数会返回 False。each() 函数的使用示例如下所示：

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程'
    );

    $arr = each($array);
    echo '数组 $array 中第'.$arr[0].'个元素的值为：'.$arr[1].'<br>';
    $arr = each($array);
    echo '数组 $array 中第'.$arr['key'].'个元素的值为：'.$arr['value'].'<br>';
    $arr = each($array);
    echo '数组 $array 中第'.$arr['key'].'个元素的值为：'.$arr['value'].'<br>';
    $arr = each($array);
    var_dump($arr);
?>
```

运行结果如下：

数组 $array 中第 0 个元素的值为：C 语言中文网
数组 $array 中第 1 个元素的值为：http://c.biancheng.net/php/
数组 $array 中第 2 个元素的值为：PHP 教程
bool(false)

each() 函数与我们介绍的《list() 函数》和《while 循环》相结合可以实现对数组的遍历，示例代码如下：

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程',
        'each() 函数'
    );

    reset($array);
    while (list($key, $value) = each($array)) {
        echo '数组 $array 中第 '.$key.' 个元素的值为：'.$value.'<br>';
    }
?>
```

运行结果如下：

数组 $array 中第 0 个元素的值为：C 语言中文网
数组 $array 中第 1 个元素的值为：http://c.biancheng.net/php/
数组 $array 中第 2 个元素的值为：PHP 教程
数组 $array 中第 3 个元素的值为：each() 函数

提示：在使用 while 循环、list() 函数和 each() 函数遍历数组时，为了避免出错需要先使用 reset() 函数将数组内部指针指向数组的第一个元素。