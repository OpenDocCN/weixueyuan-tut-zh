# PHP preg_replace()：执行一个正则表达式的搜索和替换

> 原文：[`c.biancheng.net/view/6249.html`](http://c.biancheng.net/view/6249.html)

字符串的替换是字符串操作中非常重要的内容之一。对于一些比较复杂的字符串替换操作，可以通过正则表达式的替换函数 preg_replace() 来完成。

PHP 中的 preg_replace() 函数可以执行正则表达式的搜索和替换，是一个强大的字符串替换处理函数，该函数的语法格式如下：

preg_replace($pattern, $replacement, $subject [, $limit = -1 [, &$count]])

参数说明如下：

*   $pattern：要搜索的模式，可以使一个字符串或字符串数组；
*   $replacement：用于替换的字符串或字符串数组。如果这个参数是一个字符串，并且 $pattern 是一个数组，那么所有的模式都使用这个字符串进行替换。如果 $pattern 和 $replacement 都是数组，每个 $pattern 使用 $replacement 中对应的元素进行替换。如果 $replacement 中的元素比 $pattern 中的少，多出来的 $pattern 使用空字符串进行替换。
*   $subject：要进行搜索和替换的字符串或字符串数组，如果 $subject 是一个数组，搜索和替换回在 $subject 的每一个元素上进行, 并且返回值也会是一个数组。
*   $limit：可选参数，每个模式在每个 $subject 上进行替换的最大次数。默认是 -1（无限）。
*   $count：可选参数，如果指定，将会被填充为完成的替换次数。

如果 $subject 是一个数组，preg_replace() 函数会返回一个数组，其他情况下返回一个字符串。

如果函数 preg_replace() 搜索到匹配项，则会返回被替换后的 $subject，否则返回没有改变的 $subject。preg_replace() 函数的每个参数（除了参数 $limit）都可以是一个数组。如果参数 $pattern 和参数 $replacement 都是数组，那么该函数将以其键名在数组中出现的顺序来进行处理。如果发生错误，则返回 NULL。

参数 $replacement 中可以包含后向引用 \\n 或 $n，语法上首选后者。每个这样的引用将被匹配到的第 n 个捕获子组捕获到的文本替换。n 可以是 0-99，\\0 和 $0 代表完整的模式匹配文本。

捕获子组的序号计数方式为：代表捕获子组的左括号从左到右，从 1 开始数。如果要在 $replacement 中使用反斜线，必须使用 4 个（"\\\\" 因为这首先是 php 的字符串，经过转义后是两个，再经过正则表达式引擎后才被认为是一个原文反斜线）。

当在替换模式下工作并且后向引用后面紧跟着需要是另外一个数字（比如：在一个匹配模式后紧接着增加一个原文数字），不能使用 \\1 这样的语法来描述后向引用。比如，\\11 将会使 preg_replace() 不能理解你希望的是一个 \\1 后向引用紧跟一个原文 1，还是一个 \\11 后向引用后面不跟任何东西。这种情况下解决方案是使用 ${1}1。这创建了一个独立的 $1 后向引用，一个独立的原文 1。

当使用被弃用的 e 修饰符时，这个函数会转义一些字符（即：'、"、\ 和 NULL）然后进行后向引用替换。当这些完成后请确保后向引用解析完后没有单引号或双引号引起的语法错误（比如：'strlen(\'$1\')+strlen("$2")')。确保符合 PHP 的字符串语法，并且符合 eval 语法。因为在完成替换后，引擎会将结果字符串作为 php 代码使用 eval 方式进行评估并将返回值作为最终参与替换的字符串。

【示例】使用 preg_replace() 函数替换字符串。

```

<?php
    $string = 'c biancheng net';
    $pattern = '/(\w+).(\w+).(\w+)/i';
    $replacement = 'http://$1.$2.$3/php/';
    echo preg_replace($pattern, $replacement, $string);
?>
```

运行结果如下：

http://c.biancheng.net/php/