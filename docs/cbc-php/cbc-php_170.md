# PHP 打开和关闭目录

> 原文：[`c.biancheng.net/view/7908.html`](http://c.biancheng.net/view/7908.html)

目录是计算机文件系统的重要组成部分，也可以将其看成是一种特殊的文件，所以对目录的操作同对普通文件的操作类似，在浏览之前要先打开目录，浏览完毕后同样需要关闭目录。本节我们就来介绍一下目录的打开和关闭操作。

## 1、打开目录

打开目录和打开文件虽然都是执行打开的操作，但使用的函数是不同，而且对未找到指定文件的处理结果也不同。fopen() 函数如果未找到指定的文件，则会自动创建这个文件，而打开目录的 opendir() 函数却没有那么勤劳和爽快，它会直接抛出一个错误。opendir() 函数的语法格式如下所示：

opendir(string $path[, resource $context])

其中，参数 $path 为要打开的目录路径，$context 为可选参数，用来设定目录句柄的环境，$context 是可修改目录流行为的一套选项。

opendir() 函数如果执行成功则返回目录句柄的资源（resource），失败则返回 FALSE。如果参数 $path 不是一个合法的目录或者因为权限限制或文件系统错误而不能打开目录，opendir() 函数会返回 FALSE 并产生一个 E_WARNING 级别的 PHP 错误信息。可以在 opendir() 前面加上`@`符号来抑制错误信息的输出。

【示例】使用 opendir() 函数打开指定目录。

```

<?php
    $dir = './test/';
    if(is_dir($dir)){
        $info = opendir($dir);
        var_dump($info);
    }
?>
```

运行结果如下：

resource(3) of type (stream)

示例中我们用到了一个 is_dir() 函数，该函数用来判断给定的参数是不是一个目录。

## 2、关闭目录

对一个目录的操作结束之后，需要关闭已打开的目录，即释放操作目录时所占用的资源.PHP 提供了 closedir() 函数完成关闭目录的操作，该函数的语法格式如下。

closedir([resource $dir_handle])

其中，参数 $dir_handle 为可选参数，表示目录句柄的资源（使用 opendir() 函数打开的目录资源）。

【示例】使用 closedir() 函数关闭打开的目录资源。

```

<?php
    $dir = './test/';
    if(is_dir($dir)){
        $info = opendir($dir);
        echo '目录关闭前：';var_dump($info);
        closedir($info);
        echo '<br>目录关闭后：';var_dump($info);
    }
?>
```

运行结果如下：

目录关闭前：resource(3) of type (stream)
目录关闭后：resource(3) of type (Unknown)