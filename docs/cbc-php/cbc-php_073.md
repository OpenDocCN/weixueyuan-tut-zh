# PHP next()与 prev()：向上/下移动数组指针

> 原文：[`c.biancheng.net/view/7417.html`](http://c.biancheng.net/view/7417.html)

通过前面的学习我们知道，每个数组中都有一个内部指针。使用 current() 函数可以获取内部指针指向元素的值，但是它并不能移动内部指针。本节我们就来介绍一下，如何来移动数组内部的指针。

PHP 中提供了 next() 和 prev() 两个函数，分别可以向下或向上移动数组内部的指针。

## 1、next() 函数

next() 函数可以返回数组内部指针指向的下一个元素的值，当没有更多元素时返回 FALSE。其语法格式如下：

next($array)

参数 $array 为待操作的数组。

next() 和 current() 的功能类似，只有一点区别，那就是 next() 会在返回之前将内部指针向下移动一位。这意味着它返回的是下一个数组元素的值。

【示例】使用 next() 获取数组元素的值。

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程',
        'next() 函数'
    );

    for($i = 0; $i < count($array); $i++){
        if($i == 0){
            echo current($array).'<br>';
        }else{
            echo next($array).'<br>';
        }
    }
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
PHP 教程
next() 函数

## 2、prev() 函数

prev() 函数可以返回数组内部指针指向的上一个元素的值，当没有更多元素时返回 FALSE。其语法格式如下：

prev($array)

参数 $array 为待操作的数组。

prev() 与 next() 函数的功能类似，只是 prev() 函数是将数组内部指针向上移动一位而不是向下。

【示例】使用 prev() 获取数组元素的值。

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程',
        'next() 函数'
    );

    for ($i = count($array)-1; $i >= 0; $i--) {
        if($i == count($array)-1){
            echo end($array).'<br>';
        }else{
            echo prev($array).'<br>';
        }
    }
?>
```

运行结果如下：

next() 函数
PHP 教程
http://c.biancheng.net/php/
C 语言中文网