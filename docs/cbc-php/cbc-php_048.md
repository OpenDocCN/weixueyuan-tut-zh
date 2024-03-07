# PHP 匿名函数（闭包函数）

> 原文：[`c.biancheng.net/view/6133.html`](http://c.biancheng.net/view/6133.html)

匿名函数（Anonymous functions）就是没有函数名的函数，也叫闭包函数（closures），是在 php5.3 中新增一个特性。

PHP 允许临时创建一个没有指定名称的函数。匿名函数通常用在回调函数中，同时匿名函数也可以赋值给一个变量后使用，还能像其他任何 PHP 对象那样传递，不过匿名函数仍然是函数，因此可以调用，并且可以传入参数。

注意：理论上讲，闭包和匿名函数是不同的概念，不过 PHP 将其视作相同的概念，所以提到闭包时，指的就是匿名函数，反之亦然。

## 创建匿名函数

创建匿名函数很简单，其语法结构如下所示：

function (参数列表) {
    ...
}

可以看出，匿名函数与普通函数非常类似，同样可以接受参数，并且可以有返回值，只是匿名函数在声明时会省略函数名。

【示例】下面通过一个简单的示例来演示一下如何创建匿名函数，代码如下所示：

```

<?php
    $url = function (){
        return 'http://c.biancheng.net/php/';
    };

    echo $url();
?>
```

运行结果如下：

http://c.biancheng.net/php/

通过上面的示例可以看出，匿名函数可以作为变量的值来使用。此时 PHP 会自动把此种表达式转换成内置类 Closure 的对象实例。把一个 closure 对象赋值给一个变量的方式与普通变量赋值的语法是一样的，最后也要加上分号`;`。

实际开发中，我们通常将匿名函数当做函数或方法的回调使用，在很多 PHP 内置函数中都会用到匿名函数，比如 array_map 和 preg_replace_callback，示例代码如下：

```

<?php
    $arr    = [1,2,3,4,5,6];
    $result = array_map(function ($num) {
        return $num*$num;
    }, $arr);

    echo '<pre>';
    print_r($result);
?>
```

运行结果如下：

Array
(
    [0] => 1
    [1] => 4
    [2] => 9
    [3] => 16
    [4] => 25
    [5] => 36
)

## use 关键字

使用 use 关键字，闭包函数可以实现从父级作用域中继承变量，但是从 php7.1 开始，不支持继承预定义变量和 $this。

【示例】下面通过示例来演示 use 关键字的使用，代码如下所示：

```

<?php
    function demo() {
        $website = 'C 语言中文网<br>';
        $url     = 'http://c.biancheng.net/php/';
        $func    = function() use ($website) {
            echo '$website = '.$website;
            echo '$url = '.$url;
        };
        $func();
    }
    demo();
?>
```

运行结果如下：

$website = C 语言中文网
$url =

通过运行结果可以看出，$url 并没有通过 use 关键字继承到匿名函数中，所以无法打印 $url 的值。

需要注意的是，匿名函数虽然可以继承父级作用域中的变量，但是在匿名函数中修改变量的值不会对父级作用域中的变量造成影响，示例代码如下：

```

<?php
    function demo() {
        $num  = 1;
        $func = function() use ($num) {
            $num++;
            echo '匿名函数中 $num 的值为：'.$num.'<br>';
        };
        $func();
        echo '匿名函数外 $num 的值为：'.$num;
    }
    demo();
?>
```

运行结果如下：

匿名函数中 $num 的值为：2
匿名函数外 $num 的值为：1

如果想要在修改匿名函数继承的变量的同时，同样修改其父级作用域中的变量，则需要在变量名的前面添加 & 符号，类似于函数中的引用传递。示例代码如下：

```

<?php
    function demo() {
        $num  = 1;
        $func = function() use (&$num) {
            $num++;
            echo '匿名函数中 $num 的值为：'.$num.'<br>';
        };
        $func();
        echo '匿名函数外 $num 的值为：'.$num;
    }
    demo();
?>
```

运行结果如下：

匿名函数中 $num 的值为：2
匿名函数外 $num 的值为：2