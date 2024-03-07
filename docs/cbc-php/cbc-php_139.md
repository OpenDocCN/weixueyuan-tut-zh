# PHP set_error_handler()：自定义错误处理函数

> 原文：[`c.biancheng.net/view/6257.html`](http://c.biancheng.net/view/6257.html)

PHP 中提供一个 set_error_handler() 函数，使用该函数可以指定另一个函数作为错误处理函数，其语法如下：

set_error_handler(callable $error_handler [, int $error_types = E_ALL | E_STRICT ])

#### 参数说明

1) $error_handler 是用户自定义的函数名称，除了可以传入函数名，还可以传入 NULL 重置处理程序到默认状态，还可以传入引用对象和对象方法名的数组。

用户自定义函数的格式如下：

error_handler(int $errno , string $errstr [, string $errfile [, int $errline [, array $errcontext ]]])

其中：

*   第一个参数 $errno 表示错误的级别，是一个 integer 类型；
*   第二个参数 $errstr 表示错误的信息，是一个 string 类型；
*   第三个参数 $errfile 是一个可选参数，表示发生错误的文件名，是一个 string 类型；
*   第四个参数 $errline 也是一个可选参数，表示发生错误的行号，是一个 integer 类型；
*   第五个参数 $errcontext 同样是一个可选参数（在 PHP7.2.0 之后被弃用），表示错误发生时活动符号表的 array。也就是说 $errcontext 会包含错误触发处作用域内所有变量的数组。用户的错误处理程序不应该修改错误上下文（context）。

注意：如果 set_error_handler() 函数返回 FALSE，标准错误处理程序将会继续调用。

2) $error_types 参数就像配置文件 php.ini 中 error_reporting 能够控制错误的显示一样，此参数能够用于屏蔽 $error_handler 的触发。如果没有该掩码，无论 $error_reporting 是如何设置的，$error_handler 都会在每个错误发生时被调用。

如果之前有定义过错误处理程序，则返回该程序名称；如果是内置的错误处理程序，则返回 NULL。如果指定了一个无效的回调函数，同样会返回 NULL。如果之前的错误处理程序是一个类的方法，此函数会返回一个带类和方法名的索引数组（indexed array）。

【示例】自定义一个错误处理函数，并使用它来处理程序中的错误。

```

<?php
    function error_handler($errno, $errstr, $errfile, $errline ) {
        echo "error number：".$errno."<br/>";
        echo "error msg：".$errstr."<br/>";
        echo "error file：".$errfile."<br/>";
        echo "error line：".$errline."<br/>";
        die('something error');
    }
    set_error_handler("error_handler");
    strpos();
?>
```

运行结果如下：

error number：2
error msg：strpos() expects at least 2 parameters, 0 given
error file：D:\WWW\index.php
error line：10
something error

注意：使用这种方式进行错误处理，如果没有在错误处理函数中终止程序的执行，程序将会继续执行发生错误的下一行，所以需要使用 die() 函数来终止程序的运行。

另外需要注意的是，这种错误处理方式并不能接管所有级别的程序错误，E_ERROR、E_PARSE、E_CORE_ERROR、E_CORE_WARNING、E_COMPILE_ERROR、E_COMPILE_WARNING 以及 E_STRICT 部分的错误将会以最原始的形式显示出来。

#### set_error_handler() 进阶

PHP 的异常处理机制不完善，无法自动抛出异常，用户也可使用 set_error_handler() 这种方式将异常当作错误来处理，这样用户就可以使用自定义的错误处理函数来自动捕获异常了。

【示例】使用自定义的错误处理函数来自动捕获异常。

```

<?php
    function error_handler($errno, $errstr, $errfile, $errline ) {
        echo "error number：".$errno."<br/>";
        echo "error msg：".$errstr."<br/>";
        echo "error file：".$errfile."<br/>";
        echo "error line：".$errline."<br/>";
        die('something error');
    }
    set_error_handler("error_handler");
    /* 触发异常 */
    try {
        $a = 5/0;
        echo $a;
    } catch(Exception $e) {
        echo $e->getMessage();
    }
?>
```

运行结果如下：

error number：2
error msg：Division by zero
error file：D:\WWW\index.php
error line：12
something error

当程序执行到第 12 行 `$a = 5/0;` 语句时，程序会自动捕获了这个异常，并由用户自定义的函数进行处理。