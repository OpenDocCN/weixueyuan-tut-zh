# PHP array_key_exists()：判断数组的键名或索引是否存在

> 原文：[`c.biancheng.net/view/7446.html`](http://c.biancheng.net/view/7446.html)

在 PHP 中，使用 array_key_exists() 函数可以判断给定的键名或索引是否存在于数组中。因为在一个数组中键名是唯一的，所以不需要对其数据类型进行判断。

array_key_exists() 函数的语法格式如下：

array_key_exists($key, $array)

其中，$key 为要检查的键名；$array 为一个数组，array_key_exists() 函数可以检查数组 $array 中是否存在 $key 这个键名，存在时函数返回 TRUE，不存在时返回 FALSE。

提示：array_key_exists() 函数仅能判断数组第一维的键名，不能判断多维数组里嵌套的键名。

【示例】使用 array_key_exists() 函数检查数组中是否存在指定键名。

```

<?php
    $array = array(
        'name'  =>'C 语言中文网',
        'url'   =>'http://c.biancheng.net/php/',
        'title' =>'PHP 教程',
        array(
            'id'=>'1234',
            'article'=>'array_key_exists() 函数'
        )
    );

    if(array_key_exists('url', $array)){
        echo '键名“url”存在<br>';
    }else{
        echo '键名“url”不存在<br>';
    }

    if(array_key_exists('article', $array)){
        echo '键名“article”存在<br>';
    }else{
        echo '键名“article”不存在<br>';
    }
?>
```

运行结果如下：

键名“url”存在
键名“article”不存在