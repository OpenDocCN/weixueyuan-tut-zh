# PHP preg_grep()：检测与给定模式匹配的数组元素

> 原文：[`c.biancheng.net/view/6248.html`](http://c.biancheng.net/view/6248.html)

使用正则表达式除了可以匹配字符串外，还可以匹配数组中的元素。PHP 中的 preg_grep() 函数可以搜索数组中的所有元素，并返回与正则表达式匹配的所有元素所组成的数组，函数的语法格式如下：

preg_grep($pattern, $input [, $flags = 0 ])

参数说明如下：

*   $pattern：要搜索的模式，也就是定义好的正则表达式；
*   $input：要搜索的数组；
*   $flags：可选参数，可以设置为 PREG_GREP_INVERT，这时函数会返回数组中与给定模式 $pattern 不匹配的元素组成的数组。

perg_grep() 函数将遍历 $input 数组中的每一个元素，让该元素与模式 $pattern 进行匹配，然后将匹配成功或者匹配失败的元素返回。

【示例】使用 perg_grep() 函数匹配数组中的元素。

```

<?php
    $test_arr = array(
        'C 语言中文网',
        'http://c.biancheng.net/php/',
        'php 教程',
        'abcdefg'
    );
    $preg = '/^[a-z]/';
    $preg_arr_1 = preg_grep($preg, $test_arr);
    $preg_arr_2 = preg_grep($preg, $test_arr, PREG_GREP_INVERT);
    echo '<pre>';
    var_dump($preg_arr_1);
    var_dump($preg_arr_2);
?>
```

运行结果如下：

array(3) {
  [1]=>string(27) "http://c.biancheng.net/php/"
  [2]=>string(9) "php 教程"
  [3]=>string(7) "abcdefg"
}
array(1) {
  [0]=>string(16) "C 语言中文网"
}