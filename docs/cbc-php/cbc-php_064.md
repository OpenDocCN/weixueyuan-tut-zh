# PHP str_shuffle()：随机打乱字符串

> 原文：[`c.biancheng.net/view/6147.html`](http://c.biancheng.net/view/6147.html)

在 PHP 中，可使用 str_shuffle() 函数来随机打乱一个字符串中的所有字符，其语法如下：

string str_shuffle ( string $str )

str_shuffle 返回被打乱后的字符串，参数 str 是未经打乱的原始字符串。

str_shuffle() 在打乱一个字符串时会使用任何一种可能的排序方案，所以说它是随机的。

str_shuffle() 函数的使用示例如下：

```

<?php
$str = 'abcdefg';
echo str_shuffle($str) . "<br/>";
echo str_shuffle($str) . "<br/>";
echo str_shuffle($str) . "<br/>";
?>
```

执行上述代码的结果为：

fcgdeba
fecbadg
cabdfeg

注意，每次使用 str_shuffle() 函数打乱字符串都是随机的。