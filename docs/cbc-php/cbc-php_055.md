# PHP 字符串大小写转换

> 原文：[`c.biancheng.net/view/6139.html`](http://c.biancheng.net/view/6139.html)

在 Web 开发中有很多的数据需要具有规则性，方便于管理员进行管理，所以，在存储一些数据的时侯需要对字母的大小写进行统一处理。但是，为了让用户输入方便，不会刻意的要求用户输入大写或小写，而是在存储数据时，使用程序控制将输入的内容统一大写或小写进行存储。

PHP 给我们提供了非常多的预定义函数，其中就包含关于字符串大小写转换的函数，如下表所示：

| 函数名称 | 功能 |
| strtoupper | 将字符串转化为大写 |
| strtolower | 将字符串转化为小写 |
| ucfirst | 将字符串的首字母转化为大写 |
| lcfirst | 将字符串的首字母转化为小写 |
| ucwords | 将字符串中每个单词的首字符转化为大写 |
| mb_strtoupper | 将字符串转化为大写（与 strtoupper 函数有区别） |
| mb_strtolower | 将字符串转化为小写（与 strtolower 函数有区别） |
| mb_convert_case | 按照不同的模式对字符串进行转换 |

下面就来分别介绍一下。

#### 1) strtoupper

strtoupper() 函数可以将字符串中的字母转化为大写，语法格式如下：

strtoupper($string)

其中，$string 为一个字符串类型的参数，该函数可以将参数 $string 中的字母转化为大写，并将转化后的字符串返回。

示例代码如下：

```

<?php
    $str = "http://c.biancheng.net/php/";
    $str = strtoupper($str);
    echo $str;
?>
```

运行结果如下：

HTTP://C.BIANCHENG.NET/PHP/

#### 2) mb_strtoupper

mb_strtoupper() 函数的功能与 strtoupper() 函数类似，同样可以将字符串中的字母转化为大写，并且 mb_strtoupper() 函数还可以设置参数的字符编码，其语法格式如下：

mb_strtoupper($str [, $encoding = mb_internal_encoding()])

其中，$str 是需要转化的字符串，$encoding 是一个可选参数，用来设置参数的字符编码。

和 strtoupper() 函数不同的是，$str 中的字母是通过 Unicode 字符属性来确定的。因此 mb_strtoupper() 函数不会受语言环境（locale）设置的影响，能够转化任何具有“字母”属性的字符，例如 a 变音符号（ä）。

示例代码如下：

```

<?php
    $str = "http://c.biancheng.net/php/";
    $str = mb_strtoupper($str);
    echo $str.'<br>';
    $str = "Τάχιστη αλώπηξ βαφής ψημένη γη, δρασκελίζει υπέρ νωθρού κυνός";
    $str = mb_strtoupper($str, 'UTF-8');
    echo $str;
?>
```

运行结果如下：

HTTP://C.BIANCHENG.NET/PHP/
ΤΆΧΙΣΤΗ ΑΛΏΠΗΞ ΒΑΦΉΣ ΨΗΜΈΝΗ ΓΗ, ΔΡΑΣΚΕΛΊΖΕΙ ΥΠΈΡ ΝΩΘΡΟΎ ΚΥΝΌΣ

#### 3) strtolower

strtolower() 函数可以将字符串中的字母转化为小写，语法格式如下：

strtolower($string)

其中，$string 为一个字符串类型的参数，该函数可以将参数 $string 中的字母转化为小写，并将转化后的字符串返回。

示例代码如下：

```

<?php
    $str = "HTTP://C.BIANCHENG.NET/PHP/";
    $str = strtolower($str);
    echo $str;
?>
```

运行结果如下：

http://c.biancheng.net/php/

#### 4) mb_strtolower

mb_strtolower() 函数的功能与 strtolower() 函数类似，同样可以将字符串中的字母转化为小写，并且 mb_strtolower() 函数还可以设置参数的字符编码。其语法格式如下：

mb_strtolower($str [, $encoding = mb_internal_encoding()])

其中，$str 是需要转化的字符串，$encoding 是一个可选参数，用来设置参数的字符编码。

和 strtolower() 函数不同的是，$str 中字母字符的检测是根据字符的 Unicode 属性。因此函数的行为不会受语言设置的影响，能够转换任意具有“字母”属性的字符，例如元音变音 A（Ä）。

示例代码如下：

```

<?php
    $str = "HTTP://C.BIANCHENG.NET/PHP/";
    $str = mb_strtolower($str);
    echo $str.'<br>';
    $str = "Τάχιστη αλώπηξ βαφής ψημένη γη, δρασκελίζει υπέρ νωθρού κυνός";
    $str = mb_strtolower($str, 'UTF-8');
    echo $str;
?>
```

运行结果如下：

http://c.biancheng.net/php/
τάχιστη αλώπηξ βαφής ψημένη γη, δρασκελίζει υπέρ νωθρού κυνός

#### 5) ucfirst

ucfirst 函数能够将字符串的第一个字母转化为大写。语法格式如下：

ucfirst($str)

其中，$str 为需要转化的字符串。

示例代码如下：

```

<?php
    $str = 'hello world!';
    $str = ucfirst($str);
    echo $str.'<br>';
    $str2 = 'HELLO WORLD!';
    $str2 = ucfirst(strtolower($str2));
    echo $str2;
?>
```

运行结果如下：

Hello world!
Hello world!

#### 6) lcfirst

lcfirst() 函数能够使一个字符串的第一个字符转为小写，语法格式如下：

lcfirst($str)

其中，$str 为需要转化的字符串。

示例代码如下：

```

<?php
    $str = 'Hello World!';
    $str = lcfirst($str);
    echo $str.'<br>';
    $str2 = 'HELLO WORLD!';
    $str2 = lcfirst($str2);
    echo $str2;
?>
```

运行结果如下：

hello World!
hELLO WORLD!

#### 7) ucwords

ucwords() 函数能够将字符串中每个单词的首字母转换为大写，语法格式如下：

ucwords($str [, $delimiters = "\t\r\n\f\v" ])

其中，$str 为需要转化的字符串；$delimiters 为可选参数，用来表示单词分隔符，默认是空格符、制表符、换行符、回车符、水平线以及竖线。

示例代码如下：

```

<?php
    $str = 'hello world!';
    $str = ucwords($str);
    echo $str.'<br>';
    $str2 = 'HELLO WORLD!';
    $str2 = ucwords(strtolower($str2));
    echo $str2.'<br>';
    $str3 = 'c.biancheng.net';
    $str3 = ucwords($str3,'.');
    echo $str3;
?>
```

运行结果如下：

Hello World!
Hello World!
C.Biancheng.Net

#### 8) mb_convert_case

mb_convert_case() 函数可以对字符串进行大小写转换，语法格式如下：

mb_convert_case($str, $mode [, $encoding = mb_internal_encoding()])

其中，$str 是需要转换的字符串；$mode 是转换模式，可以是 MB_CASE_UPPER、 MB_CASE_LOWER 和 MB_CASE_TITLE 的其中一个；$encoding 是参数的字符编码，可以省略。

和 strtolower()、strtoupper() 函数相比，mb_convert_case() 函数大小写转换的执行根据 Unicode 字符属性的基础。因此 mb_convert_case() 函数的行为不受语言环境（locale）设置的影响，能够转换任意具有“字母”属性的字符，例如元音变音 A（Ä）。

示例代码如下：

```

<?php
    $str = "c.biancheng.net";
    $str = mb_convert_case($str, MB_CASE_UPPER, "UTF-8");
    echo $str.'<br>';
    $str = mb_convert_case($str, MB_CASE_LOWER, "UTF-8");
    echo $str.'<br>';
    $str = mb_convert_case($str, MB_CASE_TITLE, "UTF-8");
    echo $str.'<br>';
?>
```

运行结果如下：

C.BIANCHENG.NET
c.biancheng.net
C.biancheng.net