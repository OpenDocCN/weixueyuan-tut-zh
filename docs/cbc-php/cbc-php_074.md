# PHP end()：将数组内部指针指向最后一个元素

> 原文：[`c.biancheng.net/view/7420.html`](http://c.biancheng.net/view/7420.html)

PHP 中的 end() 函数可以将数组内部的指针指向数组的最后一个元素，并返回最后一个元素的值，如果数组为空则返回 FALSE。

end() 函数的语法格式如下：

end($array)

参数 $array 为待操作的数组。

【示例】使用 end() 函数获取数组的组后一个元素。

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'PHP 教程',
        'end() 函数'
    );
    $array2 = [];

    echo '数组的最后一个元素是：'.end($array).'<br>';
    var_dump(end($array2));
?>
```

运行结果如下：

数组的最后一个元素是：end() 函数
bool(false)