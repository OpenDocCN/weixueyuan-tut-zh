# PHP mkdir()：创建目录

> 原文：[`c.biancheng.net/view/7919.html`](http://c.biancheng.net/view/7919.html)

有时侯我们需要在服务器上创建目录。比如创建以当天日期为名字的目录来备份数据，或者创建以注册用户名为名字的目录来存放用户注册信息文件等。在 PHP 中可以使用 mkdir() 函数来创建一个新的目录，函数的语法格式如下：

mkdir(string $pathname[, int $mode = 0777[, bool $recursive = false[, resource $context]]])

参数说明如下：

*   $pathname：要创建的目录路径（包含新目录的名称）；
*   $mode：可选参数，用来设定目录的权限，由四个数组组成，默认是 0777（最大的访问权限），不过 $mode 在 Windows 下会被忽略；
*   $recursive：可选参数，为 true 时允许递归创建由 $pathname 所指定的多级嵌套目录，默认为 false；
*   $context：在 PHP 5.0.0 中增加了对上下文（Context）的支持。

组成 $mode 参数的四个数字的含义如下所示：

*   第一个数字通常是 0；
*   第二个数字规定所有者的权限；
*   第三个数字规定所有者所属的用户组的权限；
*   第四个数字规定其他所有人的权限。

$mode 参数中，除第一个数字外，其它三个数字的取值范围如下（如需设置多个权限，可以将对应权限的数字相加）：

*   1 => 执行权限；
*   2 => 写权限；
*   4 => 读权限。

【示例】使用 mkdir() 函数来创建一个新目录。

```

<?php
    $dir = './test/ttt';
    if(is_dir($dir)){
        echo "该目录以存在！";
    }else{
        if(mkdir($dir,0777,true)) echo '目录创建成功！';
    }
?>
```

运行上面的代码，即可在当前目录下创建一个名为 test 的目录，并在 test 目录中创建一个名为 ttt 的目录。

需要注意是，使用 mkdir() 函数创建目录时，目录名不能与已经存在的目录名相同，如果出现了同样的目录名，程序将会报错，如下所示：

Warning: mkdir(): File exists in D:\WWW\index.php on line 6