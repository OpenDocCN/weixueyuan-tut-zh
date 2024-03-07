# PHP fgets()和 fgetss()：逐行读取文件

> 原文：[`c.biancheng.net/view/7793.html`](http://c.biancheng.net/view/7793.html)

PHP 中提供了两个函数可以实现逐行读取文件，分别是 fgets() 和 fgetss()，下面来分别介绍一下这两个函数。

## 1、fgets() 函数

fgets() 函数用于一次读取一行数据。函数的语法格式如下：

fgets(resource $handle[, int $length])

其中参数 $handle 是被打开的文件；参数 $length 为可选参数，用来设置读取的数据长度。函数能够实现从指定文件 $handle 中读取一行并返回长度最大值为 $length-1 个字节的字符串。在遇到换行符、EOF 或者读取了 $length-1 个字节后停止。如果忽略 $length 参数，则默认读取 1k（1024 字节）长度。

【示例】使用 fgets() 函数逐行读取文件的内容并输出。

```

<?php
    $handle = @fopen("./test.txt", "r");
    if ($handle) {
        while (($info = fgets($handle, 1024)) !== false) {
            echo $info.'<br>';
        }
        fclose($handle);
    }                                
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/

## 2、fgetss() 函数

fgetss() 函数是 fgets() 函数的变体，用于读取一行数据，同时 fgetss() 函数会过滤掉读取内容中的 HTML 和 PHP 标记，函数的语法格式如下：

fgetss(resource $handle[, int $length[, string $allowable_tags]])

参数说明如下：

*   $handle：为被打开的文件；
*   $length：可选参数，用来设置要读取的数据长度；
*   $allowable_tags：可选参数，用来指定哪些标记不被去掉。

注意：fgetss() 函数在 PHP7.3 及之后的版本中已经弃用。

【示例】分别使用 fgets() 函数和 fgetss() 函数读取 index.html 文件并输出结果，看一看有什么区别。

```

<?php
    echo '-------使用 fgets() 函数的输出结果：-------<br>';
    $handle = @fopen("index.html", "r");
    if ($handle) {
        while (!feof($handle)) {
            $buffer = @fgets($handle, 4096);
            echo htmlentities($buffer,ENT_QUOTES,"UTF-8").'<br>';
        }
        fclose($handle);
    }
    echo '-------使用 fgetss() 函数的输出结果：-------<br>';
    $handle = @fopen("index.html", "r");
    if ($handle) {
        while (!feof($handle)) {
            $buffer = @fgetss($handle, 4096);
            echo $buffer.'<br>';
        }
        fclose($handle);
    }
?>
```

运行结果如下：

-------使用 fgets() 函数的输出结果：-------
<h1>C 语言中文网</h1>
<p>http://c.biancheng.net/</p>
-------使用 fgetss() 函数的输出结果：-------
C 语言中文网
http://c.biancheng.net/