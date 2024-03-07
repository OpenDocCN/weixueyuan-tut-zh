# 正则表达式匹配汉字或中文

正则匹配中文汉字根据页面编码不同而略有区别：
GBK/GB2312 编码：[x80-xff>]+ 或 [xa1-xff]+
UTF-8 编码：[x{4e00}-x{9fa5}]+/u

以下以 PHP 为例进行匹配：
<?php
   $str = "学习 php 是一件快乐的事。";
   preg_match_all("/[x80-xff]+/", $str, $match);
   //UTF-8 使用：
   //preg_match_all("/[x{4e00}-x{9fa5}]+/u", $str, $match);
   print_r($match);
?>

输出：
Array
(
    [0] => Array
        (
            [0] => 学习
            [1] => 是一件快乐的事。
        )

)