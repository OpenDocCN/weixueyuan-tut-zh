# PHP file_get_contents()：将文件读入一个字符串

> 原文：[`c.biancheng.net/view/7858.html`](http://c.biancheng.net/view/7858.html)

PHP 中的 file_get_contents() 函数可以将文件的内容读取到一个字符串中，函数的语法格式如下：

file_get_contents(string $filename[, bool $use_include_path = false[, resource $context[, int $offset = -1[, int $maxlen]]]])

参数说明如下：

*   $filename：要读取的文件的名称；
*   $use_include_path：可选参数，用来设定是否想要在 include_path 中搜索该文件，默认为 FALSE；
*   $context：可选参数，用来表示使用 stream_context_create() 函数创建的有效的上下文资源，如果不需要自定义 context，可以用 NULL 来忽略；
*   $offset：可选参数，用来设定文件中开始读取的位置。注意，不能对远程文件使用该参数；
*   $maxlen：可选参数，用来设定读取的字节数，默认是读取文件的全部内容。

注意：file_get_contents() 函数执行失败时，可能返回 Boolean 类型的 FALSE，也可能返回一个非布尔值（如空字符）。所以一般使用 `===` 运算符测试此函数的返回值。

【示例】使用 file_get_contents() 函数读取文件。

```

<?php
    $file = 'test.txt';
    $filestr = file_get_contents($file);
    if($filestr){
      echo $filestr;
    }else{
      echo '读取失败！';
    }
?>
```

运行结果如下：

C 语言中文网 http://c.biancheng.net/