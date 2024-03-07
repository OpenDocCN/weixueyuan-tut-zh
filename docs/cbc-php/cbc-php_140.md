# PHP 屏蔽错误

> 原文：[`c.biancheng.net/view/7643.html`](http://c.biancheng.net/view/7643.html)

在编写 PHP 代码的时候难免会出现错误，当很多错误信息堆积到一起的时候，可能会使我们感到反感，而且错误信息还会影响用户体验。最重要的是，如果这些错误被有心人士看到的话，可能对整个程序带来威胁，那么我们应该如何屏蔽 PHP 中的错误呢？

PHP 中屏蔽错误的方法有 3 中，下面就来分别介绍一下。

## 1、使用错误控制运算符：@

PHP 支持使用错误控制运算符 `@`。将其放置在一个 PHP 表达式之前，该表达式可能产生的任何错误信息都将被忽略掉。

如果用 set_error_handler() 设定了自定义的错误处理函数，这个错误处理函数仍然会被调用，而如果在出错语句前使用了 `@` 的话，错误处理函数将返回 0。

需要注意的是，`@` 运算符只对表达式有效。简单来说就是，如果能从某处得到值，就能在它前面加上 @ 运算符。例如可以在变量、函数、include 调用、常量等等之前使用 @ 运算符，但不能把它放在函数或类的定义之前，也不能用于条件结构例如 if 和 foreach 等语句前。

`@` 运算符对于可以导致程序终止的严重错误也是有效的，这意味着如果在某个不存在或者敲错了字母的函数调用前用了 `@` 来抑制错误信息，那么程序将没有任何提示的死在那里。

【示例】使用 @ 错误控制运算符屏蔽代码中的错误。

```

<?php
    $link = @mysqli_connect("127.0.0.1", "my_user", "my_password", "my_db") or die('数据库连接失败！');
?>
```

运行结果如下：

数据库连接失败！

## 2、使用 error_reporting() 函数屏蔽错误

PHP 有诸多错误级别，使用 error_reporting() 函数可以设置 PHP 会报告何种错误，函数的语法格式如下：

error_reporting([int $level])

其中参数 $level 为设置错误级别，具体可以使用的值可以在《PHP 错误级别》一节中查看。如果将 $level 设置为 0，将关闭所有 PHP 错误报告；如果设置为 -1，将返回所有的错误报告。

【示例】使用 error_reporting() 函数屏蔽代码中的错误。

```

<?php
    error_reporting(0);
    $link = mysqli_connect("127.0.0.1", "my_user", "my_password", "my_db") or die('数据库连接失败！');
?>
```

## 3、通过 display_errors 参数屏蔽错误

这种方法应该是最彻底的一种解决办法，因为前两种方法只能作用于单行或者单个文件，而这个则是作用于所有的 PHP 文件。

具体的操作方法就是，打开 php.ini 配置文件，在其中搜索 display_errors，然后将 display_errors 的值设置为 Off 即可关闭所有的 PHP 错误报告。如下所示：

; This directive controls whether or not and where PHP will output errors,
; notices and warnings too. Error output is very useful during development, but
; it could be very dangerous in production environments. Depending on the code
; which is triggering the error, sensitive information could potentially leak
; out of your application such as database usernames and passwords or worse.
; For production environments, we recommend logging errors rather than
; sending them to STDOUT.
; Possible Values:
;   Off = Do not display any errors
;   stderr = Display errors to STDERR (affects only CGI/CLI binaries!)
;   On or stdout = Display errors to STDOUT
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/display-errors
display_errors = Off