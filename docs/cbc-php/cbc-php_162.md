# PHP readfile()：读取全部文件

> 原文：[`c.biancheng.net/view/7802.html`](http://c.biancheng.net/view/7802.html)

除了可以使用《fgets()函数》逐行读取文件外，PHP 中还提供了 3 个函数可以一次性读取整个文件，分别是 readfile()、file() 和 get_file_contents()，本节我们主要来介绍一下 readfile() 函数的用法。

readfile() 函数用于读取一个文件并将其写入到输出缓冲，同时返回从文件中读入的字节数。如果出错则返回 FALSE，该函数的语法格式如下：

readfile(string $filename[, bool $use_include_path = false[, resource $context]])

参数说明如下：

*   $filename：要读取的文件名或文件路径；
*   $use_include_path：可选参数，用来设定是否想要在 include_path 中搜索该文件，默认为 FALSE；
*   $context：Stream 上下文（context）。

提示：使用 readfile() 函数，不需要打开或关闭文件，也不需要 echo 或 print 等输出语句，直接写出文件路径即可。

【示例】使用 readfile() 函数读取文件的全部内容。

```

<?php
    $file = 'test.txt';
    $info = readfile($file);
?>
```

运行结果如下：

C 语言中文网 http://c.biancheng.net/