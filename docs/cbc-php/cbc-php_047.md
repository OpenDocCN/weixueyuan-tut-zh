# PHP return：函数返回值

> 原文：[`c.biancheng.net/view/7371.html`](http://c.biancheng.net/view/7371.html)

通过前面函数的学习我们知道，函数是一个功能集合体，可以实现某些特定的功能或运算。函数运行之后的结果保留在函数内部是没有任何意义的，所以我们需要将函数的运算结果返回到调用函数的地方。

PHP 中提供了 return 语句来返回函数的运行结果，其语法格式如下：

return 返回值;

其中，“返回值”为一个可选参数，可以是一个具体的值或者表达式，也可以为空。“返回值”与 return 关键字之间需要使用空格分隔。

注意：return 语句只能返回一个参数，即只能返回一个值，不能一次返回多个值。如果要返回多个值的话，就需要在函数中定义一个数组，将返回值存储在数组中返回。

使用 return 语句时需要注意以下几点：

*   return 语句用于向“调用函数者”返回一个值，返回值后，立即结束函数运行，所以 return 语句一般都放在函数的末尾；
*   如果一个函数中存在多个 return 语句，则只会执行第 1 个；
*   return 语句也可以不返回参数，就相当于结束函数运行；
*   如果在全局作用域内使用 return 语句，则会立即终止当前运行的脚本；
*   如果使用 include 或 require 引入的脚本文件中含有 return 语句，则会返回到引入脚本的地方继续向下执行，return 之后的其它代码不再执行。

【示例】下面实现一个计算汇率的函数，并将计算好的值通过 return 返回，代码如下所示：

```

<?php
    function money($rmb, $rate=0.1438){
        $price = $rmb * $rate;
        return $price;
    }

    $dollar = money(50);
    echo '50 元人民币 = '.$dollar.' 美元';
?>
```

运行结果如下：

50 元人民币 = 7.19 美元

提示：上面示例中，我们在函数中使用 return 返回了一个变量 $price，其实返回的并不是变量本身，而是这个变量的值。所以在函数外面我们需要使用另一个变量来存储这个值。

return 不仅能返回一个变量，还可以返回一个表达式，所以上面示例中的函数我们还能写得更加简洁，如下所示：

```

<?php
    function money($rmb, $rate=0.1438){
        return $rmb * $rate;
    }
?>
```

通过上面的介绍我们知道 return 一次只能返回一个参数，如果要返回多个参数的话需要借助数组来完成，下面我们通过一个示例来演示一下：

```

<?php
    function demo(){
        $arr = array(
                'website'  => 'C 语言中文网',
                'title'    => 'PHP 教程',
                'url'      => 'http://c.biancheng.net/php/',
            );
        return $arr;
    }

    $arr = demo();
    echo '<pre>';
    print_r($arr);
?>
```

运行结果如下：

Array
(
    [website] => C 语言中文网
    [title] => PHP 教程
    [url] => http://c.biancheng.net/php/
)

另外，在 PHP7 中增加了一个新功能——声明返回值类型。与声明参数类型相似，在非严格模式下，PHP 将会尝试将返回值类型转换为声明的类型，如果转换失败会报一个 Fatal error 错误。但在严格模式下，函数的返回值必须与声明的返回类型一致（不会尝试对返回值进行类型转换），不一致时同样会报一个 Fatal error 错误。

示例如下：

```

<?php
    function sum($a, $b):float{
        return $a + $b;
    }
    var_dump(sum(114, 233));
?>
```

运行结果如下：

float(347)

在严格模式下的代码如下：

```

<?php
    declare(strict_types=1);
    function sum($a, $b):int{
        return $a + $b;
    }
    var_dump(sum(3, 7));
    var_dump(sum(9, 3.3));
?>
```

运行结果如下：

int(10)
Fatal error: Uncaught TypeError: Return value of sum() must be of the type int, ......