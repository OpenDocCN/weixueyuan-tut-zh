# PHP 回调函数

> 原文：[`c.biancheng.net/view/7377.html`](http://c.biancheng.net/view/7377.html)

所谓的回调函数，就是指调用函数时并不是向函数中传递一个标准的变量作为参数，而是将另一个函数作为参数传递到调用的函数中，这个作为参数的函数就是回调函数。通俗的来说，回调函数也是一个我们定义的函数，但是不是我们直接来调用的，而是通过另一个函数来调用的，这个函数通过接收回调函数的名字和参数来实现对它的调用。

PHP 中的回调函数与 C、Java 等语言的回调函数的作用是一模一样的，都是在函数执行的过程中，跳转到回调函数中，当回调函数执行完毕之后，再回到之前的函数处理接下来的程序。

## PHP 中回调函数的实现

在 PHP 中调用回调函数，不像 C、Java 等语言那样直接使用函数名作为函数参数，而是使用函数名对应的字符串名称来调用。

示例代码如下所示：

```

<?php
    function arithmetic($funcName, $m, $n) {
        return $funcName($m, $n);
    }
    function add($m,$n){
        return $m+$n;
    }

    $sum = arithmetic('add', 5, 9);
    echo '5 + 9 ='.$sum;
?>
```

运行结果如下：

5 + 9 =14

另外，PHP 还提供了两个内置函数 call_user_func() 和 call_user_func_array() 来对回调函数进行支持。这两个函数的区别是 call_user_func_array() 是以数组的形式接收回调函数的参数，而 call_user_func() 则是以具体的参数来接收回调函数参数的。

#### 1) call_user_func()

call_user_func 函数会把第一个参数作为回调函数来调用，其语法格式如下：

call_user_func ($callback [, $parameter, ... ])

其中，第一个参数 $callback 是被调用的回调函数，其余参数是回调函数的参数，多个参数之间使用`,`分隔。

【示例】下面通过一个简单的示例来演示一下 call_user_func 函数的使用。

```

<?php
    function arithmetic($funcName, $m, $n) {
        return call_user_func($funcName, $m, $n);
    }
    function add($m,$n){
        return $m+$n;
    }

    $sum = arithmetic('add', 7, 17);
    echo '7 + 17 ='.$sum;
?>
```

运行结果如下：

7 + 17 =24

#### 2) call_user_func_array()

call_user_func_array 函数可以调用回调函数，并使用一个数组来作为回调函数的参数，其语法格式如下：

call_user_func_array ($callback ,$param_arr)

其中，第一个参数 $callback 是被调用的回调函数，$param_arr 是一个索引数组，用来存储需要传入回调函数中的具体参数。

【示例】下面通过一个简单的示例来演示一下 call_user_func_array 函数的使用。

```

<?php
    function arithmetic($funcName, $m, $n) {
        return call_user_func_array($funcName, array($m, $n));
    }
    function add($m,$n){
        return $m+$n;
    }

    $sum = arithmetic('add', 12, 33);
    echo '12 + 33 ='.$sum;
?>
```

运行结果如下：

12 + 33 =45