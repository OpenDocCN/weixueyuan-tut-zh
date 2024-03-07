# PHP 定义数组

> 原文：[`c.biancheng.net/view/6149.html`](http://c.biancheng.net/view/6149.html)

在 PHP 中定义数组非常灵活，与其他许多编程语言中的数组不同，PHP 不需要在创建数组时指定数组的大小，甚至不需要在使用数组前先行声明，也可以在同一个数组中存储任何类型的数据。

PHP 支持一维和多维数组，可以由用户创建，也可以由一些特定的数据库处理函数从数据库查询中生成数组，以及一些函数返回数组。本节我们主要为大家介绍如何自行声明一个一维数组。

在 PHP 中声明数组可以使用以下两种方法：

*   直接为数组元素赋值即可声明数组；
*   使用 array() 函数声明数组。

使用上面两种方法声明数组时，不仅可以指定元素的值，也可以指定元素的下标，即键和值都可以自行定义。

## 1\. 直接赋值的方式声明数组

当数组中每个元素都是一个具体的值而非一个数组时，我们称这样的数组为一维数组。一维数组在数组中是最简单的一种，也是最常用的一种。

使用直接为数组元素赋值方法声明一维数组的语法格式如下所示：

$数组变量名[下标] = 值

其中下标（索引值）可以是一个字符串或一个整数，并且下标需要使用`[ ]`包裹。

示例代码如下：

```

<?php
    $array[0] = 'C 语言中文网';
    $array[1] = 'PHP 教程';
    $array[2] = 'PHP 数组';
    $array[3] = 'http://c.biancheng.net/php/';
    echo '<pre>';
    var_dump($array);
?>
```

运行结果如下：

array(4) {
  [0]=>
  string(16) "C 语言中文网"
  [1]=>
  string(10) "PHP 教程"
  [2]=>
  string(10) "PHP 数组"
  [3]=>
  string(27) "http://c.biancheng.net/php/"
}

提示：除了可以使用 var_dump() 函数打印整个数组外，还可以使用 print_r() 函数。

PHP 中数组没有大小限制，所以在上面的数组中，可以用同样的方式继续往数组中添加新元素。访问数组中的元素时可以通过“$数组变量名[下标]”的方式，示例代码如下：

```

<?php
    $array[0] = 'C 语言中文网';
    $array[1] = 'PHP 教程';
    $array[2] = 'PHP 数组';
    $array[3] = 'http://c.biancheng.net/php/';
    echo '$array[0] = '.$array[0].'<br>';
    echo '$array[1] = '.$array[1].'<br>';
    echo '$array[2] = '.$array[2].'<br>';
    echo '$array[3] = '.$array[3].'<br>';
?>
```

运行结果如下：

$array[0] = C 语言中文网
$array[1] = PHP 教程
$array[2] = PHP 数组
$array[3] = http://c.biancheng.net/php/

声明一个索引数组时，如果索引值是递增的，我们也可以不在方括号内指定具体的索引值，这时索引值默认从 0 开始依次增加。示例代码如下：

```

<?php
    $array[] = 'C 语言中文网';
    $array[] = 'PHP 教程';
    $array[] = 'PHP 数组';
    $array[] = 'http://c.biancheng.net/php/';
    echo '<pre>';
    print_r($array);
?>
```

运行结果如下：

Array
(
    [0] => C 语言中文网
    [1] => PHP 教程
    [2] => PHP 数组
    [3] => http://c.biancheng.net/php/
)

## 2\. 使用 array() 函数声明数组

声明数组的另一种方法是使用 array() 函数来新建一个数组。它接受一定数量用逗号分隔的 `key=>value` 参数对。语法格式如下所示：

$数组变量名 = array(key1 => value1, key2 => value2, ..., keyN => valueN);

示例代码如下：

```

<?php
    $array = array(0 => 'C 语言中文网', 1 => 'PHP 教程', 2 => 'PHP 数组', 3 => 'http://c.biancheng.net/php/');
    echo '<pre>';
    var_dump($array);
?>
```

运行结果如下：

array(4) {
  [0]=>
  string(16) "C 语言中文网"
  [1]=>
  string(10) "PHP 教程"
  [2]=>
  string(10) "PHP 数组"
  [3]=>
  string(27) "http://c.biancheng.net/php/"
}

如果不使用`=>`符号指定下标，则默认为索引数组。默认的索引值也是从 0 开始依次增加。示例代码如下：

```

<?php
    $array = array('C 语言中文网','PHP 教程','PHP 数组','http://c.biancheng.net/php/');
    echo '<pre>';
    var_dump($array);
?>
```

运行结果与上一示例的运行结果相同。