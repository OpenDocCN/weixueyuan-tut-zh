# PHP preg_quote()：对正则表达式进行转义

> 原文：[`c.biancheng.net/view/6252.html`](http://c.biancheng.net/view/6252.html)

PHP preg_quote() 函数用来对正则表达式字符串进行转义，也就是在特殊字符前边增加一个反斜杠`\`，其语法如下：

preg_quote($str [, $delimiter = NULL])

参数说明如下：

*   $str：正则表达式字符串；
*   $delimiter：可选参数，额外增加的需要转义的字符。如果指定了 $delimiter 参数，被指定的字符也会被转义。这通常用于转义 PCRE 函数使用的分隔符。 / 是最常见的分隔符。

preg_quote() 函数会向参数 $str 提供的每个正则表达式的字符前增加一个反斜线。这通常用于一些运行时字符串需要作为正则表达式进行匹配的时候。

正则表达式特殊字符有：`. \ + * ? [ ^ ] $ ( ) { } = ! < > | : -`。

注意： `/` 不是正则表达式特殊字符。

【示例】使用 preg_quote() 函数对字符串进行转义：

```

<?php
    $url = 'http://c.biancheng.net/php/';
    echo preg_quote($url) . "<br />";
    echo preg_quote($url, "/") . "<br />";
    $textbody = "C 语言中文网 is *very* good to study.";
    $word     = "*very*";
    echo preg_replace( "/".preg_quote($word)."/", "<i>".$word."</i>", $textbody );
?>
```

执行以上程序的结果如下：

http\://c\.biancheng\.net/php/
http\:\/\/c\.biancheng\.net\/php\/
C 语言中文网 is *very* good to study.