# PHP7 中的错误处理

> 原文：[`c.biancheng.net/view/6258.html`](http://c.biancheng.net/view/6258.html)

在过去的 PHP 中，处理致命错误几乎是不可能的。致命错误不会调用由 set_error_handler() 设置的处理方式，而是简单的停止脚本的运行。而在 PHP7 中改变了大多数错误的报告方式，不同于之前版本的错误报告机制，现在大多数错误被作为 Error 异常抛出。

这种 Error 异常可以像 Exception 异常一样被第一个匹配的 try catch 语句块所捕获。如果没有匹配的 catch 语句块，则调用异常处理函数（事先通过 set_exception_handler() 注册）进行处理。如果尚未注册异常处理函数，则报告为一个致命错误（Fatal Error）。

注意：Error 类并非继承自 Exception 类，所以不能用 `catch (Exception $e) { ... }` 来捕获 Error。可以用 `catch (Error $e) {...}`，或者通过注册异常处理函数 set_exception_handler() 来捕获 Error。

【示例】如下代码自动捕获一个致命错误。

```

<?php
    try{
        $a = new cat();
    }catch(Error $e) {
        echo 'Error 的信息：'.$e->getMessage().'<br>Error 发生的行号：'.$e->getLine();
    }
?>
```

运行结果如下：

Error 的信息：Class 'cat' not found
Error 发生的行号：3

在 PHP7 中实现了一个全局的所有可以通过 throw 语句抛出的异常的 throwable 基础接口，原有的 Exception 和新增的部分 Error 都实现了这个接口，以接口的方式定义了异常的继承结构，也明确了 Exception 和各种 Error 都可以被当做一种可抛出的异常来处理。

要知道的是目前 PHP7 中并不是所有的错误均实现了 throwable 接口或继承至 PHP7 新增的 Error 类，原先 PHP5 中的一些错误特别是一些 E_PARSE、E_ERROR 级别的致命错误在 PHP7 中变为了可捕获的 throwable（Exception 对象或子对象和 Error 对象或子对象），如果不进行捕获则为一个错误，如果捕获就变为一个可在程序内处理的异常（throwable）。

基于上述分析可以这样理解：PHP7 中的异常所表示的范围相对于 PHP5 更为宽泛了，既包含了 PHP5 中的 Exception，又新增了 PHP7 才加入的 Error，而 Exception 和 Error 又都是实现了 throwable 接口，所以 PHP7 之后谈到异常首先应该想到的是 throwable 而不是 Exception。

throwable 接口的层次结构如下所示：

Throwable
  ├─ Error
  │    ├─ ArithmeticError
  │    │     └─ DivisionByZeroError
  │    ├─ AssertionError
  │    ├─ CompileError
  │    │    └─ ParseError
  │    └─ TypeError
  │           └─ ArgumentCountError
  └─ Exception
         └─ ...（各种 Exception 的子类）