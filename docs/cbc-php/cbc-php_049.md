# PHP 可变函数

> 原文：[`c.biancheng.net/view/6134.html`](http://c.biancheng.net/view/6134.html)

PHP 中支持可变函数的概念（也叫变量函数），可以这样理解，如果一个变量名后有小括号`( )`，那么 PHP 就会寻找与变量值同名的函数并执行它。也就是说如果给一个变量赋不同的值，程序就会调用不同的函数。

需要注意的是，可变函数不能直接用于例如 echo、print、unset()、isset()、empty()、include、require 以及类似的语言结构，需要使用自己包装的函数来将这些结构用作可变函数。

可变函数的语法如下所示：

$name();

其中，$name 为一个变量，后面的小括号 `( )` 与调用函数时函数名后的小括号功能相同。

【示例】下面使用可变函数来调用不同的函数，并输出不同的语句。

```

<?php
    function website(){
        echo 'C 语言中文网<br>';
    }
    function url($str = ''){
        echo $str.'<br>';
    }
    function title($string){
        echo $string;
    }

    $funcname = 'website';
    $funcname();
    $funcname = 'url';
    $funcname('http://c.biancheng.net/php/');
    $funcname = 'title';
    $string = 'PHP 教程';
    $funcname($string);
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
PHP 教程

上面的示例中，同样都是在调用 $funcname() 函数，随着变量值的变化而调用了不同的函数，并输出了不同的语句，这就是可变函数的一个简单的使用示例。