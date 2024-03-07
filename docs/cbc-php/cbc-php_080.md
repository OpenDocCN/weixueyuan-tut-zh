# PHP in_array()：判断数组中是否存在某个值

> 原文：[`c.biancheng.net/view/6155.html`](http://c.biancheng.net/view/6155.html)

通过前面一节的学习我们知道，使用《array_key_exists() 函数》可以判断数组中是否包含某个键值或索引，那如果我们不知道数组的键名或索引名称，想要直接查找数组中是否存在某个值的话，可以使用 PHP 中的 in_array() 函数。

in_array() 函数可以查找数组中是否包含某个值，如果存在则返回 TRUE，不存在则返回 FALSE。语法格式如下：

in_array($needle, $array[, $strict = FALSE])

参数说明如下：

*   $needle：为待搜索的值，如果 $needle 是字符串，则在比较时区分大小写；
*   $array：为待搜索的数组；
*   $strict：为可选参数，默认为 FALSE。
    *   如果 $strict 为空或者 FALSE，则 in_array() 函数只会检查 $needle 的值是否和 $array 中的值相等；
    *   如果 $strict 的值为 TRUE，in_array() 函数除了会检查 $needle 和 $array 中的值之外，还会比较它们的类型是否相等。

提示：in_array() 函数只适用于在一维数组中查找某个元素，不会递归查找数组中每个维度的元素。

【示例】使用 in_array() 函数判断数组中是否包含某个值。

```

<?php
    $array = array(
        'name'  =>'C 语言中文网',
        'url'   =>'http://c.biancheng.net/php/',
        'title' =>'PHP 教程',
        'pid'   =>1111,
        array(
            'id'=>1234,
            'article'=>'in_array() 函数'
        )
    );

    if(in_array('1234', $array, true)){
        echo '数组中包含“1234”<br>';
    }else{
        echo '数组中不包含“1234”<br>';
    }

    if(in_array('php 教程', $array)){
        echo '数组中包含“php 教程”<br>';
    }else{
        echo '数组中不包含“php 教程”<br>';
    }

    if(in_array('1111', $array)){
        echo '数组中包含“1111”<br>';
    }else{
        echo '数组中不包含“1111”<br>';
    }

    if(in_array('1111', $array, true)){
        echo '数组中包含字符串类型的“1111”<br>';
    }else{
        echo '数组中不包含字符串类型的“1111”<br>';
    }
?>
```

运行结果如下：

数组中不包含“1234”
数组中不包含“php 教程”
数组中包含“1111”
数组中不包含字符串类型的“1111”

in_array() 函数的第一个参数除了可以是一个具体的值外，还可以是一个数组。也就是说使用 in_array() 函数还可以判断一个数组是否包含另一个数组。

【示例】使用 in_array() 函数判断一个数组是否包含另一个数组。

```

<?php
    $array = array(
        'name'  =>'C 语言中文网',
        'url'   =>'http://c.biancheng.net/php/',
        'title' =>'PHP 教程',
        array(
            'id'=>1234,
            'article'=>'in_array() 函数'
        )
    );

    $arr = array(
            'id'=>1234,
            'article'=>'in_array() 函数'
        );

    if(in_array($arr, $array)){
        echo '数组 $array 中包含数组 $arr';
    }else{
        echo '数组 $array 中不包含数组 $arr';
    }
?>
```

运行结果如下：

数组 $array 中包含数组 $arr