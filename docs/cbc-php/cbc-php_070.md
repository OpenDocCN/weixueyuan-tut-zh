# PHP count()和 sizeof()：获取数组长度

> 原文：[`c.biancheng.net/view/7414.html`](http://c.biancheng.net/view/7414.html)

在 PHP 中获取数组长度方法很简单，PHP 为我们提供了两个函数来计算数组的长度，分别是 count() 和 sizeof() 函数。下面我们来分别介绍一下。

## 1、count() 函数

count() 函数可以统计出数组里所有元素的数量，或对象中的属性个数，其语法格式如下：

count($array [, $mode = COUNT_NORMAL])

参数说明如下：

*   $array：为待统计的数组或对象；
*   $mode：为可选参数，可以省略。
    *   如果省略 $mode 参数，或者将其设置为 COUNT_NORMAL 或者 0，count() 函数将不检测多维数组；
    *   如果 $mode 设置为 COUNT_RECURSIVE 或者 1，count() 函数将递归计算数组中元素的个数，对于计算多维数组的元素个数尤为有用。

提示：如果 $array 既不是数组，也不是对象，count() 函数将返回 1；如果 $array 等于 NULL，则 count() 函数返回 0。

【示例】使用 count() 函数统计数组元素的个数。

```

<?php
    $arr = ['C 语言中文网','PHP 教程','http://c.biancheng.net/php/','count()函数','数组长度'];
    echo '$arr 的长度为：'.count($arr).'<br>';
    $arr2 = ['C 语言中文网','PHP 教程',['http://c.biancheng.net/php/','count()函数','数组长度']];
    echo '$arr2 的长度为：'.count($arr2).'<br>';
    echo '参数 $mode = 1 时，$arr2 的长度为：'.count($arr2, 1).'<br>';
    $str = 'http://c.biancheng.net/php/';
    echo '$str 的长度为：'.count($str).'<br>';
?>
```

运行结果如下：

$arr 的长度为：5
$arr2 的长度为：3
参数 $mode = 1 时，$arr2 的长度为：6
$str 的长度为：1

注意：代码第 6 行把 $mode 设置为 1，count() 函数会循环统计二维数组中的所有元素，此时 ['http://c.biancheng.net/php/','count()函数','数组长度'] 会被当做一个整体统计一次，它里面的元素又会被统计一次，所以最终结果为 6。

## 2、sizeof() 函数

sizeof() 函数是 count() 函数的别名，也就是所 sizeof() 函数的功能及使用方法与 count() 函数完全相同。

【示例】使用 sizeof() 函数统计数组中元素的个数。

```

<?php
    $arr = ['C 语言中文网','PHP 教程','http://c.biancheng.net/php/','sizeof()函数','数组长度'];
    echo '$arr 的长度为：'.sizeof($arr).'<br>';
    $arr2 = ['C 语言中文网','PHP 教程',['http://c.biancheng.net/php/','sizeof()函数','数组长度']];
    echo '$arr2 的长度为：'.sizeof($arr2).'<br>';
    echo '参数 $mode = 1 时，$arr2 的长度为：'.sizeof($arr2, 1).'<br>';
    $str = 'http://c.biancheng.net/php/';
    echo '$str 的长度为：'.sizeof($str).'<br>';
?>
```

运行结果如下：

$arr 的长度为：5
$arr2 的长度为：3
参数 $mode = 1 时，$arr2 的长度为：6
$str 的长度为：1