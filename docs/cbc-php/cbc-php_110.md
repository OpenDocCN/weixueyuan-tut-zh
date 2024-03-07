# PHP spl_autoload_register()与 __autoload()：自动加载机制

> 原文：[`c.biancheng.net/view/6231.html`](http://c.biancheng.net/view/6231.html)

在使用面向对象模式开发程序时，通常大家习惯为每个类都创建一个单独的 PHP 源文件。这样会很容易实现对类进行复用，同时将来维护时也很便利，这也是面向对象程序设计的基本思想之一。

在 PHP5 之前，当需要使用一个类时，只需要直接使用 include 或 require 将其包含进来即可。如果一个页面需要使用多个类，就不得不在脚本页面开头编写一个长长的包含文件的列表。将本页面需要的类文件全部包含进来，这样处理不仅烦琐，而且容易出错。

PHP 提供了 spl_autoload_register() 和 __autoload() 函数来实现类的自动加载功能，这可以节省编程的时间。下面就来分别介绍一下。

## 1、__autoload() 函数

__autoload() 是系统函数，名称是固定的，而且该函数没有方法体，需要我们自定义方法体。另外，该函数是自动调用的，当我们 new 一个类时，如果当前源文件中找不到这个类，PHP 则会自动调用 __autoload() 函数，并将类名传递给 __autoload() 函数。

还有一点需要注意的是，__autoload() 函数在当前源文件中只能定义一次。语法格式如下：

function __autoload($class){
    // 方法体
}

其中 $class 为要加载的类名。

提示：想要使用 __autoload() 函数自动加载类文件，类文件的名称需要与类名相同，另外一个类文件中只能定义一个类。

【示例】使用 __autoload() 函数自动加载类文件。

```

<?php
    function __autoload($class){
        $file = './'.$class.'.php';
        include_once($file);
    }

    $obj = new Demo();
?>
```

运行上面的代码，会自动加载同目录下的 Demo.php 文件，Demo.php 中的代码如下所示：

```

<?php
    class Demo{
    }
?>
```

提示：__autoload() 函数自 PHP7.2.0 起已被弃用，可以使用 spl_autoload_register() 函数代替。

## 2、spl_autoload_register() 函数

spl_autoload_register() 函数可以指定一个函数来替代 __autoload() 函数的功能。语法格式如下：

spl_autoload_register([$autoload_function [, $throw = true [, $prepend = false ]]])

参数说明如下：

*   $autoload_function：要替代 __autoload() 函数的函数名称，也可以是一个匿名函数。如果没有提供任何参数，则自动注册 autoload 的默认实现函数 spl_autoload()；
*   $throw：用来设置 $autoload_function 无法成功注册时，spl_autoload_register() 函数是否抛出异常；
*   $prepend：如果是 true，则 spl_autoload_register() 函数会添加 $autoload_function 函数到队列之首，否则添加到队列尾部。

注意：与 __autoload() 函数不同，spl_autoload_register() 可以多次定义。

【示例】使用 spl_autoload_register() 函数指定另一个函数来替代 __autoload() 函数。

```

<?php
    spl_autoload_register('loadClass');
    function loadClass($class){
        $file = './'.$class.'.php';
        include_once($file);
    }

    $obj = new Demo();
?>
```

运行上面的代码同样需要上面示例中用到的 Demo.php 文件。