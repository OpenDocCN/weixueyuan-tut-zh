# PHP reset()：将数组内部指针指向数组的第一个元素

> 原文：[`c.biancheng.net/view/7426.html`](http://c.biancheng.net/view/7426.html)

通过前面的学习我们知道，可以使用《end() 函数》将数组内部的指针指向数组最后一个元素，那么如果我们需要将内部指针指向第一个元素呢？

PHP 中的 reset() 函数可以将数组中的内部指针指向第一个元素，并返回第一个数组元素的值，如果数组为空则返回布尔值 False。

值得一提的是，在使用 foreach 语句对数组遍历操作时，它会自动把指针指向数组第一个元素，然后才开始遍历，所以就不必使用 reset() 函数。如果我们使用 each() 函数遍历数组，就需要使用 reset() 将数组指针复位到起始位置。

reset() 函数的语法格式如下：

reset($array)

参数 $array 为待操作的数组。

【示例】使用 reset() 函数将数组内部指针指向数组的第一个元素。

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程'
    );

    echo current($array).'<br>';
    echo next($array).'<br>';
    echo next($array).'<br>';
    var_dump(next($array));
    echo '<br>';
    echo reset($array).'<br>';
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
PHP 教程
bool(false)
C 语言中文网