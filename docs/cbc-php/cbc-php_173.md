# PHP rmdir()：删除目录

> 原文：[`c.biancheng.net/view/7920.html`](http://c.biancheng.net/view/7920.html)

同普通文件类似的，如果确认某个目录已经不会被使用了，那么就可以把这个目录删除。在 PHP 中可以使用 rmdir() 函数来删除指定的目录，该函数的语法格式如下所示：

rmdir(string $dirname[, resource $context])

其中，参数 $dirname 为要删除的目录路径；$context 为可选参数，用来规定文件句柄的环境。

注意：使用 rmdir() 函数删除指定目录时，这个目录必须是空的，而且要有相应的权限。函数执行成功时返回 TRUE，执行失败则返回 FALSE，如果删除一个不为空的目录还会产生一个 E_WERNING 级别的错误。

【示例】使用 rmdir() 函数删除指定的目录。

```

<?php
    $dir = './test';
    if(is_dir($dir)){
        if(rmdir($dir)) echo '目录删除成功！';
    }else{
        echo "目录不存在！";
    }
?>
```

运行上面的代码，首先要确定 test 目录是空的，否则就会出现下面的错误：

Warning: rmdir(./test): Directory not empty in D:\WWW\index.php on line 4

如果要删除一个不为空的目录该怎么做呢？我们可以遍历这个目录下的所有文件和文件夹，并用递归的方式来逐个删除这个目录下的所有文件和文件夹，下面就通过示例代码来演示以下：

```

<?php
    function deldir($path){
        //如果是目录则继续
        if(is_dir($path)){
            //扫描一个文件夹内的所有文件夹和文件并返回数组
            $p = scandir($path);
            //如果 $p 中有两个以上的元素则说明当前 $path 不为空
            if(count($p)>2){
                foreach($p as $val){
                    //排除目录中的.和..
                    if($val !="." && $val !=".."){
                        //如果是目录则递归子目录，继续操作
                        if(is_dir($path.$val)){
                            //子目录中操作删除文件夹和文件
                            deldir($path.$val.'/');
                        }else{
                            //如果是文件直接删除
                            unlink($path.$val);
                        }
                    }
                }
            }
        }
        //删除目录
        return rmdir($path);
    }
    //设置需要删除的文件夹
    $path = "./test/";
    //调用函数，传入路径
    deldir($path);
?>
```