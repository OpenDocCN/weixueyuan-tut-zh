# PHP current()：返回数组的当前元素

> 原文：[`c.biancheng.net/view/6158.html`](http://c.biancheng.net/view/6158.html)

在 PHP 中，每个数组都有一个内部的指针指向它“当前的”单元（元素），这个指针最初指向的是当前数组中的第一个单元。

通过 current() 函数，就可以获取内部指针指向元素的值，它的语法格式如下：

current($array)

参数 $array 表示被操作的数组。

current() 函数可以返回当前内部指针指向的数组元素的值，但它并不会移动指针，如果需要移动指针的话需要与其它函数配合使用；如果内部指针指向超出了数组的末端，current() 函数会返回 FALSE。

提示：PHP 中还有一个 pos() 函数与 current() 函数的功能完全相同，可以将 pos() 函数当作是 current() 函数的别名。

【示例】使用 current() 函数获取内部指针指向的数组元素的值。

```

<?php
    $array = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'current() 函数'
    );

    echo current($array).'<br>';
    echo pos($array).'<br>';
    next($array);
    echo current($array).'<br>';
    next($array);
    next($array);
    var_dump(current($array));
?>
```

运行结果如下：

C 语言中文网
C 语言中文网
http://c.biancheng.net/php/
bool(false)

上面的示例代码中我们使用到了 next() 函数，它的功能与 current() 函数类似，但是它可以将内部指针向下数组的下一个元素，这里了解即可，我们后面会详细介绍。

在 PHP 中与 current() 函数类似的可以操作数组内部指针的函数还有很多，如下所示：

*   end()：将内部指针指向数组中的最后一个元素；
*   next()：将内部指针指向数组中的下一个元素；
*   prev()：将内部指针指向数组中的上一个元素；
*   reset()：将内部指针指向数组中的第一个元素；
*   each()：返回当前元素的键名和键值，并将内部指针指向数组中的下一个元素。