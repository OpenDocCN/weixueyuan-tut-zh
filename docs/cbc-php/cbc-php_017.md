# PHP 可变变量

> 原文：[`c.biancheng.net/view/7261.html`](http://c.biancheng.net/view/7261.html)

PHP 中有一个特殊的变量——可变变量。所谓可变变量就是一个变量以另外一个变量的值作为变量名。这种语法形式是 PHP 的特殊语法，其他语言中很少见。

一个可变变量获取了一个普通变量的值作为这个可变变量的变量名。利用这一特性我们可以动态的设置和使用一个变量的变量名。

声明可变变量的方法非常简单，只需要在变量名前加上至少两个“$”符号即可，示例代码如下：

```

<?php
    $demo = 'string';
    $$demo = 'C 语言中文网';
    echo $string.'<br>';
    $name = 'PHP 入门教程';
    $str = 'name';
    echo $$str.'<br>';
    $url = 'http://c.biancheng.net/php/';
    $website = 'url';
    $link = 'website';
    echo $$$link;
?>
```

运行结果如下：

C 语言中文网
PHP 入门教程
http://c.biancheng.net/php/

可变变量还可以用于数组，不过在此之前必须先解决一个模棱两可的问题，那就是当写下 $$a[1] 时，PHP 解析器需要知道你是想要 $a[1] 作为一个变量呢，还是想要 $$a 作为一个变量并取出该变量中索引为 [1] 的值。

解决这个问题的语法是使用“{ }”将变量包裹，比如第一种情况用 ${$a[1]}；第二种情况用 ${$a}[1]，示例代码如下：

```

<?php
    $demo = 'C 语言中文网';
    $test = 'http://c.biancheng.net/php/';
    $arr = array('demo', 'test');
    echo ${$arr[0]}.'<br>'.${$arr[1]}.'<br>';

    $arr2 = array('PHP 教程', 'PHP 可变变量');
    $str = 'arr2';
    echo ${$str}[0].'<br>'.${$str}[1];
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
PHP 教程
PHP 可变变量

上面的示例中用到了数组方面的知识，不明白也没关系，大家只需要理解可变变量的使用即可，有关数组的知识我们会在后面详细介绍。