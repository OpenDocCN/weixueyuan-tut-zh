# PHP str_repeat()：重复一个字符串

> 原文：[`c.biancheng.net/view/6146.html`](http://c.biancheng.net/view/6146.html)

在 PHP 中，使用 str_repeat() 函数可以重复一个字符串，语法如下：

string str_repeat ( string $str , int $n )

参数 str 表示要被重复的字符串，n 表示重复次数；该函数返回 str 重复 n 次后的结果。

注意，n 的值必须大于等于 0。如果 n 被设置为 0，那么函数将返回一个空字符串。

str_repeat() 使用示例如下：

```

<?php
$str = "PHP 是世界上最好的编程语言！<br />";
echo "重要的事情说三遍：<br />" . str_repeat($str, 3);
?>
```

以上代码的执行结果为：

重要的事情说三遍：
PHP 是世界上最好的编程语言！
PHP 是世界上最好的编程语言！
PHP 是世界上最好的编程语言！