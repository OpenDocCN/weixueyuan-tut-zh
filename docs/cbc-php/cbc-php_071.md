# PHP 数组排序函数

> 原文：[`c.biancheng.net/view/7461.html`](http://c.biancheng.net/view/7461.html)

在使用 PHP 进行编程时，有时需要对数组进行排序，通常我们使用的对数组进行排序的方法有冒泡排序、快速排序等等。

我们除了可以自定义一些数组排序的函数外，PHP 还内置了多种对数组进行排序的函数，这些函数允许用户在数组内部对元素进行排列。通过排序可以对数据进行合理的管理，提高程序的执行效率。下面我们就来介绍一下 PHP 中的数组排序函数。

PHP 中常用的数组排序函数如下表所示：

| 函数 | 说明 |
| sort() | 对数组元素进行升序排序（从小到大）。 |
| rsort() | 对数组元素进行降序排序（从大到小）。 |
| asort() | 对数组元素进行升序排序（从小到大），并保持索引关系。 |
| arsort()  | 对数组元素进行降序排序（从大到小），并保持索引关系。 |
| ksort() | 按照数组的键值对数组进行升序排序（从小到大），并保持索引关系。 |
| krsort() | 按照数组的键值对数组进行降序排序（从大到小），并保持索引关系。 |

## 1、sort() 函数

sort() 函数可以将数组进行升序排序（从小到大，由低到高），语法格式如下：

sort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，默认为“SORT_REGULAR”，用来定义函数排序的模式。$sort_flags 可以设置为下面这些值：
    *   0 或 SORT_REGULAR：正常比较数组元素，不改变其类型（默认值）；
    *   1 或 SORT_NUMERIC：将数组元素当作数字来比较；
    *   2 或 SORT_STRING：将数组元素当作字符串来比较
    *   3 或 SORT_LOCALE_STRING：根据当前的区域（locale）设置来把数组元素当作字符串比较，可以用 setlocale() 来改变。
    *   4 或 SORT_NATURAL：和 natsort() 类似对每个数组元素以“自然的顺序”对字符串进行排序，是 PHP5.4.0 中新增的。
    *   5 或 SORT_FLAG_CASE：能够与 SORT_STRING 或 SORT_NATURAL 合并（OR 位运算），不区分大小写排序字符串。

提示：sort() 函数会删除 $array 中原有的键名并为其赋与新的键名，而不是仅仅将数组元素重新排序。函数执行成功时会返回 TRUE，失败时会返回 FALSE。

【示例】使用 sort() 函数对数组进行排序。

```

<?php
    $num = array(10, 23, 5, 12, 84, 16);
    echo '<pre>';
    sort($num);
    print_r($num);
    $language = array(
        'a' =>'http://c.biancheng.net/php/',
        'b' =>'PHP',
        'c' =>'Java',
        'd' =>'Golang',
        'e' =>'Python'
    );
    sort($language, SORT_FLAG_CASE);
    print_r($language);
?>
```

运行结果如下：

Array
(
    [0] => 5
    [1] => 10
    [2] => 12
    [3] => 16
    [4] => 23
    [5] => 84
)
Array
(
    [0] => Golang
    [1] => Java
    [2] => PHP
    [3] => Python
    [4] => http://c.biancheng.net/php/
)

需要注意的是，在对混合类型的数组进行排序时需要特别小心，sort() 函数很可能会返回一个不可预知的结果。

## 2、rsort() 函数

rsort() 函数同样可以对数组进行升序排序（从小到大，由低到高），但它不会修改原数组中的键名。可以将 rsort() 函数看作是 sort() 函数的增强版。

rsort() 函数主要用于那些特别重视值和索引之间关系的数组，其语法格式如下：

asort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，用来定义函数排序的模式，默认值为“SORT_REGULAR”。$sort_flags 的取值与 sort() 函数中的 $sort_flags 参数相同。

【示例】使用 asort() 函数对数组进行排序。

```

<?php
    $language = array(
        'a' =>'http://c.biancheng.net/php/',
        'b' =>'PHP',
        'c' =>'Java',
        'd' =>'Golang',
        'e' =>'Python'
    );
    echo '<pre>';
    asort($language, SORT_FLAG_CASE);
    print_r($language);
?>
```

运行结果如下：

Array
(
    [d] => Golang
    [c] => Java
    [b] => PHP
    [e] => Python
    [a] => http://c.biancheng.net/php/
)

## 3、rsort() 函数

rsort() 函数函数可以对数组进行降序排序（从大到小，由高到低），与 sort() 函数类似 rsort() 函数也会删除数组原有的键名，并为其重新赋值。其语法格式如下：

rsort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，用来定义函数排序的模式，默认值为“SORT_REGULAR”。$sort_flags 的取值与 sort() 函数中的 $sort_flags 参数相同。

【示例】使用 rsort() 函数对数组进行排序。

```

<?php
    $language = array(
        'a' =>'Linux',
        'b' =>'PHP',
        'c' =>'Java',
        'd' =>'Golang',
        'e' =>'Python'
    );
    echo '<pre>';
    rsort($language, 0);
    print_r($language);
?>
```

运行结果如下：

Array
(
    [0] => Python
    [1] => PHP
    [2] => Linux
    [3] => Java
    [4] => Golang
)

## 4、arsort() 函数

arsort() 函数可以对数组进行降序排序（从大到小，由高到低），但它不会修改原数组中的键名。与 rsort() 函数类似 arsort() 函数同样适用于那些特别重视值和索引之间关系的数组，其语法格式如下：

arsort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，用来定义函数排序的模式，默认值为“SORT_REGULAR”。$sort_flags 的取值与 sort() 函数中的 $sort_flags 参数相同。

【示例】使用 arsort() 函数对数组进行排序。

```

<?php
    $language = array(
        'a' =>'Linux',
        'b' =>'PHP',
        'c' =>'Java',
        'd' =>'Golang',
        'e' =>'Python'
    );
    echo '<pre>';
    arsort($language, 0);
    print_r($language);
?>
```

运行结果如下：

Array
(
    [e] => Python
    [b] => PHP
    [a] => Linux
    [c] => Java
    [d] => Golang
)

## 5、ksort() 函数

ksort() 函数可以根据数组的键值对数组进行升序排序（从小到大，由低到高），不会修改数组的键名。该函数主要用于关联数组，语法格式如下：

ksort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，用来定义函数排序的模式，默认值为“SORT_REGULAR”。$sort_flags 的取值与 sort() 函数中的 $sort_flags 参数相同。

【示例】使用 ksort() 函数对数组进行排序。

```

<?php
    $info = array(
        'id'       => 1234,
        'url'      => 'http://c.biancheng.net/php/',
        'language' => 'PHP',
        'article'  => '数组排序函数'
    );
    echo '<pre>';
    ksort($info, 0);
    print_r($info);
?>
```

运行结果如下：

Array
(
    [article] => 数组排序函数
    [id] => 1234
    [language] => PHP
    [url] => http://c.biancheng.net/php/
)

## 6、krsort() 函数

krsort() 函数可以根据数组的键值对数组进行降序排序（从大到小，由高到低），不会修改数组的键名。该函数主要用于关联数组，语法格式如下：

krsort($array [, $sort_flags = SORT_REGULAR])

参数说明如下：

*   $array：为要排序的数组。
*   $sort_flags：为可选参数，用来定义函数排序的模式，默认值为“SORT_REGULAR”。$sort_flags 的取值与 sort() 函数中的 $sort_flags 参数相同。

【示例】使用 krsort() 函数对数组进行排序。

```

<?php
    $info = array(
        'id'       => 1234,
        'url'      => 'http://c.biancheng.net/php/',
        'language' => 'PHP',
        'article'  => '数组排序函数'
    );
    echo '<pre>';
    krsort($info, 0);
    print_r($info);
?>
```

运行结果如下：

Array
(
    [url] => http://c.biancheng.net/php/
    [language] => PHP
    [id] => 1234
    [article] => 数组排序函数
)