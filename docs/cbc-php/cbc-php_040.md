# PHP include 和 require：文件包含语句

> 原文：[`c.biancheng.net/view/6125.html`](http://c.biancheng.net/view/6125.html)

文件包含是指将另一个源文件的全部内容包含到当前源文件中进行使用，通常也称为引入外部文件。引用外部文件可以减少代码的重用性，是 PHP 编程的重要技巧。

PHP 中提供了 4 个非常简单却很有用的包含语句，分别是 include 语句、require 语句、include_once 语句和 require_once 语句。这 4 种语句在使用上有一定的区别。下面分别进行详细讲解。

## 1\. include 语句

使用 include 语句包含外部文件时，只有代码执行到 include 语句时才会将外部文件包含进来，当所包含的外部文件发生错误时，系统会给出一个警告，而整个 PHP 程序会继续向下执行。

include 语句的语法格式如下：

include('filename') 或者 include 'filename'

其中 filename 为需要包含的文件路径（相对路径和绝对路径都行），filename 为一个字符串，所以需要使用单引号`' '`或双引号`" "`包裹起来。同时 include 后面的括号也可以省略，省略括号时 include 需要使用空格与后面的 filename 分隔开。

为了方便演示，这里我们准备一个 demo.php 文件，并在其中简单的定义一个 $str 变量，如下所示：

```

<?php
    $str = 'http://c.biancheng.net/php/';
?>
```

【示例】使用 include 语句来包含 demo.php 文件，代码如下所示：

```

<?php
    include './demo.php';
    echo $str;
?>
```

运行结果如下：

http://c.biancheng.net/php/

## 2\. require 语句

require 语句的使用方法与 include 语句类似，都是实现对外部文件的引用。在 PHP 文件执行之前，PHP 解析器会用被引用文件的全部内容替换 require 语句，然后与 require 语句之外的其他语句组成新的 PHP 文件，最后再按新 PHP 文件执行程序代码。

注意：因为 require 语句相当于将另一个源文件的内容完全复制到本文件中，所以一般将其放在源文件的起始位置，用于引用需要使用的公共函数文件和公共类文件等。

require 语句和 include 语句几乎完全一样，不同的是当被包含文件不存或存在错误时，require 语句会发出一个 Fatal error 错误并终止程序执行，而 include 则会发出一个 Warining 警告但程序会接着向下执行。

require 语句的语法格式为：

require(filename) 或者 require 'filename'

参数 filename 为待包含的文件路径，其特点与 include 语句中的参数一样。

【示例】使用 require 语句来包含上面定义的 demo.php 文件，代码如下所示：

```

<?php
    require './demo.php';
    echo $str;
?>
```

运行结果如下：

http://c.biancheng.net/php/

## 3\. include_once 语句

include_once 语句和 include 语句类似，唯一的区别就是如果包含的文件已经被包含过，就不会再次包含。include_once 可以确保在脚本执行期间同一个文件只被包含一次，以避免函数重定义、变量重新赋值等问题。

下面我们调整一下上面定义的 demo.php 文件，如下所示：

```

<?php
    echo 'C 语言中文网<br>';
    echo 'http://c.biancheng.net/php/';
?>
```

【示例】使用 include_once 语句来包含 demo.php 文件，代码如下所示：

```

<?php
    include_once './demo.php';
    include_once './demo.php';
    include_once './demo.php';
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/

## 4\. require_once 语句

require_once 语句是 require 语句的延伸，它的功能与 require 语句基本类似，不同的是，在应用 require_once 语句时会先检查要包含的文件是不是已经在该程序中的其他地方被包含过，如果有，则不会再次重复包含该文件。

【示例】使用 require_once 语句来包含 demo.php 文件，代码如下所示：

```

<?php
    require_once './demo.php';
    require_once './demo.php';
    require_once './demo.php';
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/