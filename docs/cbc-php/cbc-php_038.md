# PHP goto：跳转到程序的指定位置

> 原文：[`c.biancheng.net/view/7276.html`](http://c.biancheng.net/view/7276.html)

goto 操作符可以用来跳转到程序中的另一位置。该目标位置可以用目标名称加上冒号来标记（例如 name: ），而跳转指令是 goto 之后接上目标位置的标记。

PHP 中 goto 操作符的使用有一定限制：

*   goto 跳转的目标位置只能位于同一个文件和作用域内；
*   goto 无法跳出一个函数或类方法，也无法跳入到另一个函数；
*   goto 无法跳入到任何循环或者 switch 结构中；
*   goto 可以跳出循环或者 switch。

我们通常是使用 goto 代替 break 来跳出嵌套的多层循环，goto 本身并没有跳出循环的作用，但其跳转到指定位置的作用使得其可以代替 break 来跳出循环。

和其他语言一样，PHP 中并不鼓励滥用 goto，滥用 goto 会导致程序的可读性严重下降。

提示：goto 操作符仅在 PHP5.3 及以上版本有效。

goto 操作符的语法格式如下：

goto 标志;
    //代码块;
标志:
     //代码块;

其中，goto 操作符后面的“标志”为需要程序跳转到的目标位置，而定义目标位置时需要在目标位置名称的后面加上一个`:`。

【示例】下面通过一个简单的示例演示一下 goto 操作符的使用：

```

<?php
    echo 'PHP goto 操作符<br>';
    goto a;
    echo 'C 语言中文网<br>';

    a:
    echo 'http://c.biancheng.net/php/<br>';
?>
```

运行结果如下：

PHP goto 操作符
http://c.biancheng.net/php/

【示例】使用 goto 操作符代替 break 跳出循环：

```

<?php
    for($i=0; $i<100; $i++) {
        echo '$i = '.$i.'<br>';
        if($i == 3){
            goto end;
        }
    }

    end:
    echo '跳出到 end 目标位置！';
?>
```

运行结果如下：

$i = 0
$i = 1
$i = 2
$i = 3
跳出到 end 目标位置！