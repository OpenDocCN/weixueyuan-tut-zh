# PHP preg_match()：执行正则表达式匹配

> 原文：[`c.biancheng.net/view/6246.html`](http://c.biancheng.net/view/6246.html)

PHP 中的 preg_match() 函数可以根据正则表达式对字符串进行搜索匹配，函数的语法格式如下：

preg_match($pattern,$subject [, &$matches [, $flags = 0 [, $offset = 0 ]]])

参数说明如下：

*   $pattern：要搜索的模式，也就是编辑好的正则表达式；
*   $subject：要搜索的字符串；
*   $matches：可选参数（数组类型），如果提供了 $matches，它将被填充为搜索结果。 $matches[0] 包含完整模式匹配到的文本， $matches[1] 包含第一个捕获子组匹配到的文本，以此类推；
*   $flags：可选参数，$flags 可以被设置为 PREG_OFFSET_CAPTURE，如果传递了这个标记，对于每一个出现的匹配，返回时都会附加上字符串偏移量（相对于目标字符串的）；
*   $offset：可选参数，用于指定从目标字符串的哪个位置开始搜索（单位是字节）。

preg_match() 函数可以返回 $pattern 的匹配次数，它的值将是 0 次（不匹配）或 1 次，因为 preg_match() 在第一次匹配后将会停止搜索。

【示例】使用 preg_match() 函数搜索一个字符串。

```

<?php
    $subject = "abcdefghijkdef";
    $pattern_1 = '/def/';
    $num = preg_match($pattern_1, $subject, $matches_1,PREG_OFFSET_CAPTURE,8);
    echo '<pre>';
    var_dump($matches_1);
    var_dump($num);   //匹配次数为 1 次
    $pattern_2 = '/def$/';
    $num = preg_match($pattern_2, $subject, $matches_2, PREG_OFFSET_CAPTURE, 3);
    var_dump($matches_2);
?>
```

运行结果如下：

array(1) {
  [0]=>array(2) {
    [0]=>string(3) "def"
    [1]=>int(11)
  }
}
int(1)
array(1) {
  [0]=>array(2) {
    [0]=>string(3) "def"
    [1]=>int(11)
  }
}

对于第一次匹配，将从字符串的第 8 位搜索与 $pattern_1 匹配的子串，$matches_2 数组中包含匹配得到的子串和其出现在目标字符串中的位置。

注意第二次正则表达式与第一次的正则表达式不同，其中加了一个定位符号`$`，表示匹配字符串结尾处的位置。