# PHP 错误级别

> 原文：[`c.biancheng.net/view/6255.html`](http://c.biancheng.net/view/6255.html)

PHP 中定义了许多不同级别的错误，如使用了未定义的变量会报出一个 notice 级别的错误，实例化一个未定义的类则会报出 fatal error 级别的错误。

可在 php.ini 配置文件中定义错误级别，如`error_reporting=E_ALL|E_STRICT`会设置最严格的错误级别，在代码中也可使用`error_reporting(E_ALL)`等来定义错误级别。

PHP 中的错误类型如下表所示。

| 值 | 常量 | 说明 |
| 1 | E_ERROR | 致命的运行时错误，一般是不可恢复的情况，例如内存分配导致的问题，后果是导致脚本终止、不再继续运行。 |
| 2 | E_WARNING | 运行时警告（非致命错误），仅给出提示信息，但是脚本不会终止运行。 |
| 4 | E_PARSE | 编译时语法解析错误，仅由分析器产生。 |
| 8 | E_NOTICE | 运行时通知，表示脚本遇到可能会表现为错误的情况，但是在可以正常运行的脚本里面也可能会有类似的通知。 |
| 16 | E_CORE_ERROR | 在 PHP 初始化启动过程中发生的致命错误，类似 E_ERROR，但是是由 PHP 引擎核心产生的。 |
| 32 | E_CORE_WARNING | PHP 初始化启动过程中发生的警告（非致命错误），类似 E_WARNING ，但是是由 PHP 引擎核心产生的。 |
| 64 | E_COMPILE_ERROR | 致命编译时错误，类似 E_ERROR，但是是由 Zend 脚本引擎产生的。 |
| 128 | E_COMPILE_WARNING | 编译时警告（非致命错误），类似 E_WARNING，但是是由 Zend 脚本引擎产生的。 |
| 256 | E_USER_ERROR | 用户产生的错误信息，类似 E_ERROR，但是是由用户自己在代码中使用 PHP 函数 trigger_error() 来产生的。 |
| 512 | E_USER_WARNING | 用户产生的警告信息，类似 E_WARNING，但是是由用户自己在代码中使用 PHP 函数 trigger_error() 来产生的。 |
| 1024 | E_USER_NOTICE | 用户产生的通知信息，类似 E_NOTICE，但是是由用户自己在代码中使用 PHP 函数 trigger_error() 来产生的。 |
| 1024 | E_STRICT | 启用 PHP 对代码的修改建议，以确保代码具有最佳的互操作性和向前兼容性。 |
| 2048 | E_RECOVERABLE_ERROR | 可被捕捉的致命错误，表示发生了一个可能非常危险的错误，但是还没有导致 PHP 引擎处于不稳定的状态。如果该错误没有被用户自定义句柄捕获，将成为一个 E_ERROR，从而使脚本终止运行。 |
| 8192 | E_DEPRECATED | 运行时通知，启用后将会对在未来版本中可能无法正常工作的代码给出警告。 |
| 16384 | E_USER_DEPRECATED | 用户产生的警告信息，类似 E_DEPRECATED，但是是由用户自己在代码中使用 PHP 函数 trigger_error() 来产生的。 |
| 30719 | E_ALL | E_STRICT 除外的所有错误和警告信息。 |

表格中的值（数值或者符号）用于建立一个二进制位掩码，制定要报告的错误信息。可以使用按位运算符来组合这些值或者屏蔽某些类型的错误。

注意，在 php.ini 之中，只有 '|'、'~'、'!'、'^' 和 '&' 会正确解析。

在正式环境中，可能会发生各种未知的错误，这时可以定义 error_reporting(0)，这样就能屏蔽错误了，用户不会在页面看到错误信息，而当排查错误时，依然可到 PHP 的执行错误日志中寻找相关信息。