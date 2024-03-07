# PHP file()：把整个文件读入一个数组中

> 原文：[`c.biancheng.net/view/7806.html`](http://c.biancheng.net/view/7806.html)

file() 函数也是读取整个文件的内容，与前面介绍的《readfile() 函数》不同的是，file() 函数会将文件的内容按行存放到数组中（包括换行符在内）。如果成功则返回这个数组，失败则返回 FALSE。file() 函数的语法格式如下：

file(string $filename[, int $flags = 0[, resource $context]])

参数说明如下：

*   $filename：要读取的文件名称或路径；
*   $flags：可选参数，flags 可以是以下一个或多个常量：
    *   FILE_USE_INCLUDE_PATH：在 include_path 中查找文件；
    *   FILE_IGNORE_NEW_LINES：在数组每个元素的末尾不要添加换行符；
    *   FILE_SKIP_EMPTY_LINES：跳过空行。
*   $context：使用 stream_context_create() 函数创建的上下文资源。

函数返回的结果数组中的每一行都将包括行尾，除非使用了 FILE_IGNORE_NEW_LINES，因此如果不希望行尾出现，可以使用 rtrim() 函数。

注意：在读取 Mac 电脑中或由其创建的文件时，如果 PHP 不能正确的识别行结束符，可以在运行时配置中启用 auto_detect_line_endings 选项。

【示例】使用 file() 读取文件并输出。

```

<?php
    $file = 'test.txt';
    $arr  = file($file, FILE_IGNORE_NEW_LINES);
    $arr2 = file($file);
    echo '<pre>';
    var_dump($arr);
    var_dump($arr2);
?>
```

运行结果如下：

array(2) {
  [0]=>
  string(16) "C 语言中文网"
  [1]=>
  string(23) "http://c.biancheng.net/"
}
array(2) {
  [0]=>
  string(18) "C 语言中文网
"
  [1]=>
  string(23) "http://c.biancheng.net/"
}