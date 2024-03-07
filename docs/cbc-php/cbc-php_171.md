# PHP readdir()和 scandir()：读取目录下的文件及文件夹

> 原文：[`c.biancheng.net/view/7910.html`](http://c.biancheng.net/view/7910.html)

前面我们介绍了使用《opendir() 函数》打开一个目录资源，正确打开目录后就可以获取该目录下的文件及文件夹信息了。在 PHP 中提供了 readdir() 和 scandir() 两个函数来读取指定目录下的内容，下面我们来分别介绍以下。

## 1、readdir() 函数

使用 readdir() 函数可以获取目录中下一个文件或目录的名称，函数的语法格式如下：

readdir([resource $dir_handle])

其中，$dir_handle 为可选参数，表示通过 opendir() 函数打开的目录资源。

【示例】使用 readdir() 函数读取指定目录中的的文件及文件夹。

```

<?php
    $dir = 'D:';
    if(is_dir($dir)){
        $info = opendir($dir);
        while (($file = readdir($info)) !== false) {
            echo $file.'<br>';
        }
        closedir($info);
    }
?>
```

运行结果如下：

6C4816922082
CloudMusic
code
Download
Drivers
Gopath
install
System Volume Information
uploads
www

## 2、scandir() 函数

除了使用函数 readdir() 可以获取目录下的文件及文件夹名称外，使用 scandir() 函数也可以列出指定目录中的文件及文件夹名称，scandir() 函数的语法格式如下：

scandir(string $directory[, int $sorting_order[, resource $context]])

参数说明如下：

*   $directory：要读取的目录；
*   $sorting_order：为可选参数，用来设定默认的排序方式；
    *   如果设置为 SCANDIR_SORT_DESCENDING 或者 1，则将返回结果按字母降序排列；
    *   如果设置为 SCANDIR_SORT_NONE，则返回未排列的结果。
*   $context：为可选参数，规定目录句柄的环境。$context 是可修改目录流的行为的一套选项。

scandir() 函数执行成功会返回一个包含有文件及文件夹名称的数组，如果执行失败则返回 FALSE。如果参数 $directory 不是个目录，则返回布尔值 FALSE 并生成一条 E_WARNING 级的错误。

【示例】使用 scandir() 函数读取指定目录下的文件及文件夹名称。

```

<?php
    $dir = 'D:';
    if(is_dir($dir)){
        $arr1 = scandir($dir);
        $arr2 = scandir($dir, 1);
    }
    echo "<pre>";
    print_r($arr1);
    print_r($arr2);
?>
```

运行结果如下：

Array
(
    [0] => 6C4816922082
    [1] => CloudMusic
    [2] => Download
    [3] => Drivers
    [4] => Gopath
    [5] => System Volume Information
    [6] => code
    [7] => install
    [8] => uploads
    [9] => www
)
Array
(
    [0] => www
    [1] => uploads
    [2] => install
    [3] => code
    [4] => System Volume Information
    [5] => Gopath
    [6] => Drivers
    [7] => Download
    [8] => CloudMusic
    [9] => 6C4816922082
)