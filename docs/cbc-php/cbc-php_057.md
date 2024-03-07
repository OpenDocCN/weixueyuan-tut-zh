# PHP 字符串替换

> 原文：[`c.biancheng.net/view/6141.html`](http://c.biancheng.net/view/6141.html)

在 PHP 中，可以对一个字符串中的特定字符或子串进行替换，这是非常常用的功能。

## str_ireplace() 和 str_replace() 函数

str_ireplace() 和 str_replace 使用新的字符串替换原来字符串中指定的特定字符串，str_replace 区分大小写，str_ireplace() 不区分大小写，两者语法相似。

str_ireplace() 的语法如下：

mixed str_ireplace ( mixed $search , mixed $replace , mixed $subject [, int &$count ] )

该函数返回一个字符串或者数组。该字符串或数组是将 subject 中全部的 search 用 replace 替换（忽略大小写）之后的结果。参数 count 表示执行替换的次数。

使用示例如下：

```

<?php
$str = 'hello,world,hello,world';
$replace = 'hi';
$search = 'hello';
echo str_ireplace($search, $replace, $str);
?>
```

执行以上代码的输出结果为：

hi,world,hi,world

## substr_replace() 函数

substr_replace() 函数的语法如下：

mixed substr_replace ( mixed $string , mixed $replacement , mixed $start [, mixed $length ] )

substr_replace() 在字符串 string 的副本中将由 start 和可选的 length 参数限定的子字符串使用 replacement 进行替换。

如果 start 为正数，替换将从 string 的 start 位置开始。如果 start 为负数，替换将从 string 的倒数第 start 个位置开始。

如果设定了 length 参数并且为正数，就表示 string 中被替换的子字符串的长度。如果设定为负数，就表示待替换的子字符串结尾处距离 string 末端的字符个数。如果没有提供此参数，那么默认为 strlen(string)（字符串的长度）。当然，如果 length 为 0，那么这个函数的功能为将 replacement 插入 string 的 start 位置处。

该函数的使用示例如下：

```

<?php
$str = 'hello,world,hello,world';
$replace = 'hi';
echo substr_replace($str, $replace, 0,5);
?>
```

以上代码的执行结果为：

hi,world,hello,world