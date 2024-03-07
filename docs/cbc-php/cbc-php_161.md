# PHP fread()：读取文件（任意长度）

> 原文：[`c.biancheng.net/view/7796.html`](http://c.biancheng.net/view/7796.html)

PHP 中的 fread() 函数可以在打开的文件中读取指定长度的数据，也可以安全用于二进制文件。在区分二进制文件和文本文件的系统上（如 Windows）打开文件时，fread() 函数的 mode 参数要加上`b`。该函数的语法格式如下：

fread(resource $handle, int $length)

其中 $handle 为通过 fopen() 函数创建的文件资源；$length 为最多读取 $length 个字节。

fread() 函数可以从文件中读取指定长度的数据，当读取了 $length 个字节或者读取到了文件末尾（EOF）时函数会停止读取，并返回所读取到的字符串。如果读取失败则返回 FALSE。

注意，fread() 函数会从文件指针的当前位置读取。使用 ftell() 可以查找指针的当前位置，使用 rewind() 可以回放指针位置。

【示例】使用 fread() 函数读取文件的内容并输出。

```

<?php
    $filename = "./test.txt";
    $handle = fopen($filename, "r");
    $contents = fread($handle, '16');
    echo '从文件中读取 16 个字符长度：'.$contents.'<br>';
    rewind($handle);
    $contents = fread($handle, filesize($filename));
    echo '读取全部的文件内容：'.$contents;
    fclose($handle);
?>
```

运行结果如下：

从文件中读取 16 个字符长度：C 语言中文网
读取全部的文件内容：C 语言中文网 http://c.biancheng.net/

上面示例中使用到了一个 filesize() 函数，它的作用是获取文件大小，在 fread() 函数中的作用就是读取整个文件。

当从任何不是普通本地文件读取时，例如在读取从远程文件或 popen() 以及 fsockopen() 返回的流时，我们无法获取这些资源的大小，所以需要循环读取，然后将数据合并在一起，如下所示：

```

<?php
    $handle = fopen("http://c.biancheng.net/", "r");
    $contents = '';
    while (!feof($handle)) {
      $contents .= fread($handle, 8192);
    }
    fclose($handle);
    $contents = iconv("gb2312", "utf-8", $contents);    // 转换字符编码
    echo $contents;
?>
```