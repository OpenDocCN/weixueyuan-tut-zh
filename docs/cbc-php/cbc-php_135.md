# PHP 异常处理（Exception）

> 原文：[`c.biancheng.net/view/6253.html`](http://c.biancheng.net/view/6253.html)

异常（Exception）处理用于在指定的错误发生时改变脚本的正常流程，是在 PHP5 中的增加的一个重要特性。异常处理是一种可扩展、易维护的错误处理统一机制，并提供了一种新的面向对象的错误处理方式。

在 Java、C# 及 Python 等语言中很早就提供了这种异常处理机制，如果你对哪一种语言中的异常处理熟悉，那对 PHP 中提供的异常处理机制也就不会陌生了。

异常处理的加入使得在 PHP 程序中排查错误、捕获异常更加简单容易，也使得 PHP 程序在健壮性方面有很大改善和提高。异常处理在 PHP 中的具体体现就是，PHP 提供了一个名叫 Exception 的类完成对 PHP 程序异常的处理，这个类包含了一些处理异常的函数，这些函数可以捕获程序异常和错误。

本节我们就来介绍一下 PHP 中的异常处理类及类函数的用法。

## PHP 中的异常处理类

PHP 中提供了内置的异常处理类——Exception，该类中常用的成员函数如下所示：

*   getMessage()：返回异常的消息内容；
*   getCode()：以数字形式返回异常代码；
*   getFile()：返回发生异常的文件名；
*   getLine()：返回发生错误的代码行号；
*   getTrace()：返回 backtrace() 数组；
*   getTraceAsString()：返回已格式化成字符串的、由函数 getTrace() 函数所产生的信息；
*   __toString()：产生异常的字符串信息，它可以重载。注意，该函数最前部是两个下划线。

下面代码是 Exception 类的完整代码，从这个类的定义可以看出哪些属性和方法（成员函数）在用户派生的子类中是可以访问和继承的。

```

Exception {
    /* 属性 */
    protected string $message ;
    protected int $code ;
    protected string $file ;
    protected int $line ;
    /* 方法 */
    public __construct ([ string $message = "" [, int $code = 0 [, Throwable $previous = NULL ]]] )
    final public getMessage ( void ) : string
    final public getPrevious ( void ) : Throwable
    final public getCode ( void ) : int
    final public getFile ( void ) : string
    final public getLine ( void ) : int
    final public getTrace ( void ) : array
    final public getTraceAsString ( void ) : string
    public __toString ( void ) : string
    final private __clone ( void ) : void
}
```

## 捕获程序中的异常

在 PHP 中想要捕获程序中的异常，需要使用 try catch 语句和 throw 关键字来实现。try catch 语句和流程控制语句类似，所以可以通过 try catch 语句来实现一种另类的条件选择结构，而 throw 关键字则可以抛出一个异常。try catch 语句的语法格式如下：

try{
    // 可能出现异常或错误的代码，比如文件操作、数据库操作等
}catch(Exception $e){    // $e 为一个异常类的对象
    // 输出错误信息
}

需要进行异常处理的代码都必须放入 try 代码块内，以便捕获可能存在的异常。每一个 try 至少要有一个与之对应的 catch。使用多个 catch 可以捕获不同的类所产生的异常。

当 try 代码块不再抛出异常或者找不到 catch 能匹配所抛出的异常时，PHP 代码就会在跳转到最后一个 catch 的后面继续执行。

在 PHP 代码中所产生的异常可以被 throw 语句抛出并被 catch 语句捕获。当然，PHP 允许在 catch 代码块内再次抛出（throw）异常。

当一个异常被抛出时，其后的代码不会再继续执行，而 PHP 就会尝试继续查找第一个能与之匹配的 catch。如果一个异常没有被捕获，而且又没用使用 set_exception_handler() 作相应的处理的话，将会产生一个严重的错误，并且输出 UncaughtException...（未捕获异常）的提示信息。

【示例】使用 try catch 和 throw 捕获程序中的异常。

```

<?php
    try{
        $err = '抛出异常信息，并跳出 try 语句块';
        if(is_dir('./test')){
            echo '这里是一些可能会发生异常的代码';
        }else{
            throw new Exception($err, 12345);   // 抛出异常
        }
        echo '上面抛出异常的话，这行代码将不会执行，转而执行 catch 中的代码。<br>';
    }catch(Exception $e){
        echo '捕获异常：'.$e->getMessage().'<br>错误代码：'.$e->getCode().'<br>';
    }
    echo '继续执行 try catch 语句之外的代码';
?>
```

运行结果如下：

捕获异常：抛出异常信息，并跳出 try 语句块
错误代码：12345
继续执行 try catch 语句之外的代码

示例代码中，在 try 语句块中试着判断当前目录下是否存在名为 test 的目录，如果不存在这个目录，那么就会执行第 7 行的代码，通过关键字 throw 抛出异常。这个异常是一个 Exception 类的对象，通过 new 关键字生成，并且用错误信息 $err 和错误代码 12345 初始化该对象，以便后面 catch 该异常时（代码第 11 行），可以获取这些信息。

一旦抛出异常，那么 try 语句块中剩下的代码就不再继续执行，程序流程转至相应的 catch 语句块执行，最终通过 Exception 对象调用其成员函数输出错误信息和代码。

## PHP 创建自己的异常类

在各种语言里，对异常和错误的定义不同。在 PHP 里遇到任何错误都会抛出一个错误，很少会主动抛出异常，不像 Java 语言那样会预先定义好各种异常类，当程序执行到异常处的代码时会主动抛出。

PHP 的异常处理机制并不完善，在 PHP 中想处理不可预料的异常是办不到的，必须事先定义一些异常，将各种可能出现的异常进行 if…else 判断，手动抛出异常，所以在 PHP 里经常会使用到我们自己创建的异常类。

下面定义两个异常类，都继承自 Exception 基类。

```

class emailException extends Exception{
    function __toString(){
        return "<b>email is null</b>file:".$this->getFile().',line:'. $this->getLine();
    }
}
class nameException extends Exception{
}
```

在实际业务中可根据不同需求抛出不同异常，业务代码如下：

```

function reg($reg) {
    if (empty($reg['email'])) {
        throw new emailException("emaill is null", 1);
    }
    if(empty($reg['name'])) {
        throw new nameException("name is null", 2);
     }
}
```

在执行业务代码时，需要使用 if 语句判断异常会发生的地方，然后手动抛出异常，将不同的异常分发给不同的异常类处理，如下所示：

```

try{
    $reg = array('phone'=>'1888888888');
    reg($reg);
} catch(emailException $e) {
    echo $e;
} catch(nameException $e) {
    echo 'error msg:' .$e->getMessage().'error code:'.$e->getCode();
} finally {
    echo ' finally';
}
```

这段程序根据不同的情况捕获不同的异常，如果第一个 catch 捕获了异常，即使程序中仍然存在其他异常，也会跳过其他的 catch 代码块，但是不管程序中是否出现异常，最终 finally 中的语句都会执行。

执行以上程序的结果为：    

**email is null** file:/Library/WebServer/Documents/book/try.php,line:39 finally