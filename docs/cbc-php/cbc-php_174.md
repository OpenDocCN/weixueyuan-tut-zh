# PHP 遍历文件夹下所有文件和文件夹

> 原文：[`c.biancheng.net/view/7929.html`](http://c.biancheng.net/view/7929.html)

在进行 PHP 编程时，有时需要对服务器的某个目录下面的文件和文件夹进行浏览，我们通常称之为遍历目录，但是系统提供的《scandir() 函数》只能获取指定目录的文件和文件夹，对于这个目录下那些不为空的文件夹中的内容则无法获取。如果我们要获取目录下的所有文件和文件夹信息的话该怎么做呢？

其实，遍历目录的实现非常简单，与我们前面借助递归和 scandir() 函数来实现《删除目录》的功能类似，下面就通过一个具体的示例来演示一下遍历目录的具体实现方法。

```

<?php
    function selectdir($dir, $level=0) {
         //首先先读取文件夹
        $temp=scandir($dir);
        $level++;
        //遍历文件夹
        foreach($temp as $v){
            $a=$dir.'/'.$v;
            if(is_dir($a)){//如果是文件夹则执行
                if($v=='.' || $v=='..'){//判断是否为系统隐藏的文件.和..  如果是则跳过
                   continue;
                }
                echo str_repeat('——', $level-1).$v,"<br/>";
                selectdir($a, $level);//因为是文件夹所以再次调用 selectdir，把这个文件夹下的文件遍历出来
            }else{
                echo str_repeat('——', $level-1).$v,"<br/>";
            }
        }
    }

    $path = "./joo";
    selectdir($path);
?>
```

运行结果如下：

LICENSE.txt
README.txt
administrator
——cache
————index.html
——components
————com_actionlogs
——————actionlogs.php
——————models
————————actionlog.php
————————actionlogs.php
————————fields
——————————extension.php
——————————logcreator.php
——————————logsdaterange.php
——————————logtype.php
——————————plugininfo.php
plugins
——actionlog
——authentication
————cookie
——————cookie.php
——————cookie.xml
web.config.txt

这里我们只截取了一部分输出结果，通过上面的输出结果可以看出，成功输出了指定目录下的所有文件和文件夹，并按照层级关系将之排列整齐。