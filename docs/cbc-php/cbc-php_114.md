# PHP preg_match_all()：执行全局正则表达式匹配

> 原文：[`c.biancheng.net/view/6247.html`](http://c.biancheng.net/view/6247.html)

在 PHP 中还有一个和 preg_match() 函数极为类似的函数——preg_match_all()，该函数可以搜索字符串中所有可以和正则表达式匹配的结果，语法格式如下：

preg_match_all($pattern, $subject [, &$matches [, $flags = PREG_PATTERN_ORDER [, $offset = 0 ]]])

参数说明如下：

*   $pattern：要搜索的模式，也就是定义好的正则表达式；
*   $subject：要搜索的字符串；
*   $matches：可选参数（多维数组），用来存放所有匹配的结果, 数组排序通过 $flags 指定；
*   $flags：可选参数，可以结合下面几个标记使用（注意不能同时使用 PREG_PATTERN_ORDER 和 PREG_SET_ORDER）：
    *   PREG_PATTERN_ORDER：结果排序为 $matches[0] 保存完整模式的所有匹配，$matches[1] 保存第一个子组的所有匹配，以此类推。
    *   PREG_SET_ORDER：结果排序为 $matches[0] 包含第一次匹配得到的所有匹配（包含子组）， $matches[1] 是包含第二次匹配到的所有匹配(包含子组)的数组，以此类推。
    *   PREG_OFFSET_CAPTURE：如果这个标记被传递，每个发现的匹配返回时会增加它相对目标字符串的偏移量。注意这会改变 $matches 中的每一个匹配结果字符串元素，使其成为一个第 0 个元素为匹配结果字符串，第 1 个元素为匹配结果字符串在 subject 中的偏移量。
*   $offset：可选参数，$offset 用于从目标字符串中指定位置开始搜索（单位是字节）。

preg_match_all() 函数可以返回 $pattern 的匹配次数（可能是 0），如果发生错误则返回 FALSE。

【示例】使用 preg_match_all() 函数匹配一个字符串。

```

<?php
    $subject = "abcdefghijkdefabcedfdefxyzdef";
    $pattern_1 = '/(def)(abc)/';
    $num_1 = preg_match_all($pattern_1, $subject, $matches_1,PREG_PATTERN_ORDER);
    echo "<pre>";
    var_dump($matches_1);
    var_dump($num_1);
    $pattern_2 = '/(def)(abc)/';
    $num_2 = preg_match_all($pattern_2, $subject, $matches_2,PREG_OFFSET_CAPTURE, 3);
    var_dump($matches_2);
    var_dump($num_2);
?>
```

运行结果如下：

array(3) {
  [0]=>array(1) {
    [0]=>string(6) "defabc"
  }
  [1]=>array(1) {
    [0]=>string(3) "def"
  }
  [2]=>array(1) {
    [0]=>string(3) "abc"
  }
}
int(1)
array(3) {
  [0]=>array(1) {
    [0]=>array(2) {
      [0]=>string(6) "defabc"
      [1]=>int(11)
    }
  }
  [1]=>array(1) {
    [0]=>array(2) {
      [0]=>string(3) "def"
      [1]=>int(11)
    }
  }
  [2]=>array(1) {
    [0]=>array(2) {
      [0]=>string(3) "abc"
      [1]=>int(14)
    }
  }
}
int(1)