# PHP list()：把数组中的值赋给一组变量

> 原文：[`c.biancheng.net/view/7282.html`](http://c.biancheng.net/view/7282.html)

PHP 中 list() 可以把一个数组中的值分别赋给一组变量，像 array() 一样，它并不是真正的函数，而是语言结构。 list() 可以在单次操作内为一组（多个）变量赋值。

list() 语法结果如下：

list($var1 [, $val2, ...]);

其中 $val1、$val2 是待赋值的一组变量，多个变量之间使用逗号`,`分隔。

注意：list() 仅能用于索引数组，并且索引要从 0 开始。PHP5 里 list() 从最右边的参数开始赋值；而 PHP7 里 list() 从最左边的参数开始赋值。

下面通过一个简单的示例演示以下 list() 的使用，代码如下所示：

```

<?php
    $array = array("C 语言中文网","PHP 教程","http://c.biancheng.net/php/");
    list($a, $b, $c) = $array;
    echo '$a = '.$a.'<br>$b = '.$b.'<br>$c = '.$c;
?>
```

运行结果如下：

$a = C 语言中文网
$b = PHP 教程
$c = http://c.biancheng.net/php/

通过运行结果可以看出，list() 中的变量是按照数组的索引顺序赋值的，并且索引要从 0 开始，如果数组的索引不是从 0 开始的话，又会怎么样呢，我们来看下面的代码：

```

<?php
    $array = array(1=>"C 语言中文网",2=>"PHP 教程",3=>"http://c.biancheng.net/php/");
    list($a, $b, $c) = $array;
    echo '$a = '.$a.'<br>$b = '.$b.'<br>$c = '.$c;
?>
```

运行结果如下：

$a =
$b = C 语言中文网
$c = PHP 教程

可以看出 $a 并没有被赋值，这是因为数组中并没有索引为 0 的值，如果在使用 list() 时，并不需要数组中全部的值时，我们可以省略 list() 中的部分变量，如下面这样：

```

<?php
    $array = array("C 语言中文网", "PHP 教程", "http://c.biancheng.net/php/");
    list($a, , $c) = $array;
    echo '$a = '.$a.'<br>$b = '.$b.'<br>$c = '.$c;
?>
```

运行结果如下：

$a = C 语言中文网
$b =
$c = http://c.biancheng.net/php/

如果要将多维数组的值赋给一组变量，我们还可以将 list() 嵌套起来使用，下面以二维数组为例来演示以下，代码如下所示：

```

<?php
    $array = array(
        array('C 语言中文网', 'PHP 教程'),
        array('PHP 数组', 'http://c.biancheng.net/php/')
    );
    list(list($a, $b), list($c, $d)) = $array;
    echo '$a = '.$a.'<br>$b = '.$b.'<br>$c = '.$c.'<br>$d = '.$d;
?>
```

运行结果如下：

$a = C 语言中文网
$b = PHP 教程
$c = PHP 数组
$d = http://c.biancheng.net/php/