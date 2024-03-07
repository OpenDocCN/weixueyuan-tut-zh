# PHP fgetc()：从文件中读取一个字符

> 原文：[`c.biancheng.net/view/7787.html`](http://c.biancheng.net/view/7787.html)

在对某一个字符进行查找、替换时，就需要有针对性地对某个字符进行读取，在 PHP 中可以使用 fgetc() 函数实现此功能。该函数语法格式如下：

fgetc(resource $handle)

其中参数 $handle 为使用 fopen() 或 fsockopen() 成功打开的文件资源。

fgetc() 函数可以返回一个包含有一个字符的字符串，该字符是从 $handle 指向的文件中得到。当碰到 EOF 时返回 FALSE。

注意：fgetc() 函数可能返回布尔值 FALSE，也可能返回等同于 FALSE 的非布尔值。所以应该使用 `===` 运算符来测试此函数的返回值。

另外，fgetc() 函数可安全用于二进制对象，但不适用于读取中文字符串，因为一个中文通常占用 2~3 个字符。

【示例】使用 fgetc() 函数逐个字符的读取文件中的内容并输出。

```

<?php
    header("Content-Type: text/html;charset=utf-8");    //设置字符编码
    $handle = fopen('./test.txt', 'r');                 //打开文件
    if (!$handle) {                                     //判断文件是否打开成功
        echo '文件打开失败！';
    }
    while (false !== ($char = fgetc($handle))) {        //循环读取文件内容
        echo $char;
    }
    fclose($handle);                                    //关闭文件
?>
```

代码中用到的 test.txt 文件中的内容如下图所示：

![test.txt 文件中的内容](img/5a1beb80d196d38a277634023a404d52.png)
图：test.txt 文件中的内容
运行结果如下：

C 语言中文网 http://c.biancheng.net/