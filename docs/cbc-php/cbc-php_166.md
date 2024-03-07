# PHP file_exists()：判断文件是否存在

> 原文：[`c.biancheng.net/view/7878.html`](http://c.biancheng.net/view/7878.html)

在对一个文件进行操作之前，为了避免出错，首先应该判断这个文件是否存，因为打开一个并不存在的文件，会导致程序出错停止。

PHP 中为我们提供了一个专门的函数来做这一项工作，这个函数就是 file_exists()。file_exists() 函数可以判断文件或目录是否存在，存在时函数返回 TRUE，否则返回 FALSE，函数的语法格式如下：

file_exists(string $filename)

其中，参数 $filename 为文件或目录的路径。

【示例】使用 file_exists() 函数判断一个文件是否存在。

```

<?php
    $file = 'test.txt';
    if(file_exists($file)){
        echo $file.' 存在！';
    }else{
        echo $file.' 不存在！';
    }
?>
```

运行结果如下：

test.txt 存在！

注意：基于安全考虑，file_exists() 函数的文件名参数不能为远程文件，例如执行`file_exists('http://c.biancheng.net/index.html')`时，不论该文件是否存在，函数都会返回 FALSE。