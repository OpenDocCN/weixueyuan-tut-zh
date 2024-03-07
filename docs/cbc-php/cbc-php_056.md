# PHP 字符串查找（4 个函数）

> 原文：[`c.biancheng.net/view/6140.html`](http://c.biancheng.net/view/6140.html)

在 PHP 中，可以使用以下函数来查找字符串。

## stripos()

stripos() 用来查找字符串中某部分字符串首次出现的位置（不区分大小写）。

语法如下：

int stripos ( string $haystack , string $needle [, int $offset = 0 ] )

参数说明如下：

*   haystack：在该字符串中查找。
*   needle：needle 可以是一个单字符或者多字符的字符串。如果 needle 不是一个字符串，那么它将被转换为整型并被视为字符顺序值。
*   offset：可选的 offset 参数允许你指定从 haystack 中的哪个字符开始查找，返回的位置数字值仍然相对于 haystack 的起始位置。

返回 needle 存在于 haystack 字符串开始的位置（独立于偏移量）。同时注意字符串位置起始于 0，而不是 1。如果未发现 needle 就将返回 false。

示例如下：

```

<?php
$findme = 'c';
$mystring1 = 'xyz';
$mystring2 = 'ABC';
$pos1 = stripos($mystring1, $findme);
$pos2 = stripos($mystring2, $findme);
var_dump($pos1);
var_dump($pos2);
?>
```

执行结果为：

bool(false) int(2)

## strpos()

strpos() 用来查找字符串首次出现的位置。

语法如下：

mixed strpos ( string $haystack , mixed $needle [, int $offset = 0 ] )

strpos() 和 strrpos()、strripos() 不一样，strpos 的偏移量不能是负数。

示例如下：

```

<?php
$findme = 'c';
$findme1 = 'C';
$mystring = 'ABCabc';
$pos1 = strpos($mystring, $findme);
$pos2 = strpos($mystring, $findme1);
var_dump($pos1);
var_dump($pos2);
?>
```

上述代码的执行结果为：

int(5)int(2)

## strripos()

strripos() 用来计算指定字符串在目标字符串中最后一次出现的位置（不区分大小写）。

语法如下：

int strripos ( string $haystack , string $needle [, int $offset = 0 ] )

负数偏移量将使得查找从字符串的起始位置开始，到 offset 位置为止。

示例如下：

```

<?php
$findme = 'c';
$findme1 = 'C';
$mystring = 'ABCabcabcABC';
$pos1 = strripos($mystring, $findme);
$pos2 = strripos($mystring, $findme1);
var_dump($pos1);
var_dump($pos2);
?>
```

上述代码的执行结果为：

int(11)int(11)

## strrpos()

strrpos() 用来计算指定字符串在目标字符串中最后一次出现的位置.

语法如下：

int strrpos ( string $haystack , string $needle [, int $offset = 0 ] )

如果是负数的偏移量，将会导致查找在字符串结尾处开始的计数位置处结束。

示例如下：

```

<?php
$findme = 'c';
$findme1 = 'C';
$mystring = 'ABCabcabcABC';
$pos1 = strrpos($mystring, $findme);
$pos2 = strrpos($mystring, $findme1);
$pos3 = strrpos($mystring, $findme1,-5);
var_dump($pos1);
var_dump($pos2);
var_dump($pos3);
?>
```

上述代码的执行结果为：

int(8)int(11)int(2)