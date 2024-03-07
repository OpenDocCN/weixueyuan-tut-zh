# PHP 清除、删除 Session

> 原文：[`c.biancheng.net/view/7629.html`](http://c.biancheng.net/view/7629.html)

当使用完一个 Session 变量后，可以将其删除；当完成一个会话后，也可以将其销毁。如果用户想退出 Web 系统，就需要为他提供一个注销的功能，把他的所有信息在服务器中销毁。

删除 Session 会话的方法主要有删除单个 Session 元素、删除多个 Session 元素和结束当前会话 3 种，下面分别介绍一下。

## 1、删除单个 Session 元素

删除单个 Session 元素同数组的操作一样，直接注销 $_SESSION 数组的某个元素即可。例如，删除 $_SESSION['name'] 时，可以直接使用 unset() 函数，例如 `unset($_SESSION['name']);`。

unset() 函数可以释放指定的变量，其语法格式如下：

unset(mixed $var [, mixed $...])

其中 $var 为要释放的变量，unset() 函数可以接收多个参数，参数之间使用`,`分隔。

注意：在使用 unset() 函数删除单个 Session 元素时，要注意尽量不要省略具体的元素名，即不要一次性的注销整个 $_SESSION 数组，这样有可能会造成意想不到的错误。

【示例】使用 unset() 函数，删除指定的 Session 元素。

```

<?php
    session_start();
    echo '<pre>';
    $str = 'C 语言中文网';
    $arr = ['删除 Session','$_SESSION'];
    $_SESSION['name']  = $str;
    $_SESSION['url']   = 'http://c.biancheng.net/php/';
    $_SESSION['title'] = $arr;
    echo '定义一个 Session，如下所示：<br>';
    print_r($_SESSION);
    echo '删除 Session 中名为 title 的元素：<br>';
    unset($_SESSION['title']);
    print_r($_SESSION);
?>
```

运行结果如下：

定义一个 Session，如下所示：
Array
(
    [name] => C 语言中文网
    [url] => http://c.biancheng.net/php/
    [title] => Array
        (
            [0] => 删除 Session
            [1] => $_SESSION
        )

)
删除 Session 中名为 title 的元素：
Array
(
    [name] => C 语言中文网
    [url] => http://c.biancheng.net/php/
)

## 2、删除多个 Session 元素

如果想要一次性删除多个 Session 元素，即一次注销所有的会话变量，可以通过将一个空的数组赋值给 $_SESSION 来实现，示例代码如下：

```

<?php
    session_start();
    echo '<pre>';
    $str = 'C 语言中文网';
    $arr = ['删除 Session','$_SESSION'];
    $_SESSION['name']  = $str;
    $_SESSION['url']   = 'http://c.biancheng.net/php/';
    $_SESSION['title'] = $arr;
    echo '定义一个 Session，如下所示：<br>';
    print_r($_SESSION);
    echo '删除 Session 中的多个元素：<br>';
    $_SESSION = array();
    print_r($_SESSION);
?>
```

运行结果如下：

定义一个 Session，如下所示：
Array
(
    [name] => C 语言中文网
    [url] => http://c.biancheng.net/php/
    [title] => Array
        (
            [0] => 删除 Session
            [1] => $_SESSION
        ) )
删除 Session 中的多个元素：
Array
(
)

当然，除了可以给 $_SESSION 赋值一个空数组外，使用 session_unset() 函数也可以释放 Session 中的所有元素，函数的语法格式如下：

session_unset()

session_unset() 函数不需要传入参数，而且没有返回值。示例代码如下：

```

<?php
    session_start();
    echo '<pre>';
    $str = 'C 语言中文网';
    $arr = ['删除 Session','$_SESSION'];
    $_SESSION['name']  = $str;
    $_SESSION['url']   = 'http://c.biancheng.net/php/';
    $_SESSION['title'] = $arr;
    echo '定义一个 Session，如下所示：<br>';
    print_r($_SESSION);
    echo '使用 session_unset() 函数释放 Session 中的所有元素：<br>';
    session_unset();
    print_r($_SESSION);
?>
```

运行结果如下：

定义一个 Session，如下所示：
Array
(
    [name] => C 语言中文网
    [url] => http://c.biancheng.net/php/
    [title] => Array
        (
            [0] => 删除 Session
            [1] => $_SESSION
        )

)
使用 session_unset() 函数释放 Session 中的所有元素：
Array
(
)

## 3、结束当前会话

如果整个 Session 会话结束，可以使用 session_destroy() 函数销毁当前会话的全部数据，即彻底销毁 Session，函数的语法格式如下：

session_destroy()

session_destroy() 函数不需要传入任何参数，另外，session_destroy() 函数虽然可以销毁当前会话中的全部数据，但是不会重置 $_SESSION 数组，也不会重置 Cookie。如果需要再次使用 Session 会话，则必须重新调用 session_start() 函数。

注意：使用 $_SESSION = array() 清空 $_SESSION 数组的同时，也将这个用户在服务器端对应的 Session 文件内容清空。而使用 session_destroy() 函数时，则是将这个用户在服务器端对应的 Session 文件删除。