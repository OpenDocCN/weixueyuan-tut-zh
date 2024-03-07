# PHP 删除、复制、重命名文件

> 原文：[`c.biancheng.net/view/7884.html`](http://c.biancheng.net/view/7884.html)

在对文件进行操作时，不仅可以对文件中的数据进行操作，还可以对文件本身进行作。例如复制文件、删除文件、截取文件及为文件重命名等操作。在 PHP 中为我们提供了这些文件处理方式的标准函数，使用也非常简便。

文件的基本操作函数如下所示：

*   сoру()：复制文件
*   unlink()：删除文件
*   rename()：重命名文件或目录

下面我们来分别介绍一下这几个函数的使用方法。

## 1、сoру() 函数

сoру() 函数可以将一个文件复制（拷贝）到指定目录中，执行成功时返回 TRUE，失败时返回 FALSE。函数的语法格式如下：

copy(string $source, string $dest[, resource $context])

参数说明如下：

*   $source：源文件路径；
*   $dest：目标路径，如果文件存在，则会将其覆盖，如果 $dest 是一个 URL，若封装协议不支持覆盖已有的文件，则会复制失败；
*   $context：可选参数，表示使用 stream_context_create() 创建的有效上下文资源。

【示例】使用 сoру() 函数将文件复制到指定位置。

```

<?php
    $file    = 'test.txt';
    $newfile = 'newtest.txt';
    if(copy($file, $newfile)){
        echo '文件复制成功！';
    }else{
        echo '文件复制失败！';
    }
?>
```

运行上面的代码会将 test.txt 文件在当前目录下复制一份，并重命名为 newtest.txt，然后输出下面的内容。

文件复制成功！

## 2、unlink() 函数

unlink() 函数可以删除指定的文件，函数执行成功时返回 TRUE，失败时返回 FALSE，其语法格式如下：

unlink(string $filename[, resource $context])

其中，$filename 为要删除的文件路径；$context 为可选参数，规定文件句柄的环境。$ontext 是可修改流的行为的一套选项。

【示例】使用 unlink() 函数删除指定的文件。

```

<?php
    $file = 'newtest.txt';
    if(file_exists($file)){
        if(unlink($file)){
            echo $file.' 删除成功！';
        }else{
            echo $file.' 删除失败！';
        }
    }else{
        echo $file.' 不存在！';
    }
?>
```

运行上面的代码会删除根目录下的 newtest.txt 文件，并返回下面的内容：

newtest.txt 删除成功！

## 3、rename() 函数

rename() 函数可以重命名一个文件或者目录，成功时返回 TRUE，失败时则返回 FALSE。该函数的语法格式如下：

rename(string $oldname, string $newname[, resource $context])

其中，$oldname 为要修改的文件名；$newname 为新的文件名；$context 为可选参数，用来规定文件句柄的环境。$context 是一套可以修改流的行为的选项。

【示例】使用 rename() 函数来重命名一个文件。

```

<?php
    $file = 'test.txt';
    if(file_exists($file)){
        if(rename($file,'newtest.txt')){
            echo $file.' 重命名成功！';
        }else{
            echo $file.' 重命名失败！';
        }
    }else{
        echo $file.' 不存在！';
    }
?>
```

运行上面的代码会将根目录的 test.txt 文件重命名为 newtest.txt，并输出一下内容：

test.txt 重命名成功！

使用 rename() 函数有以下几点需要注意：

*   对于非空文件夹，只能在同一盘符下移动；
*   对于空文件夹，rename() 可以在不同盘符间移动。但是目标文件夹的父目录必须存在；
*   对于文件，rename() 也能在不同盘符之间移动。