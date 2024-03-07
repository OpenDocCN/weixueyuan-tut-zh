# PHP 设置和获取 Session

> 原文：[`c.biancheng.net/view/7627.html`](http://c.biancheng.net/view/7627.html)

在 PHP 中想要使用 Session 变量，除了必须要启动之外，还要经过设置的过程。设置和获取 Session 中的数据，都要通过访问 $_SESSION 数组来完成。

自 PHP4.1.0 起，$_SESSION 同 $_POST、$_GET 和 $_COOKIE 等一样成为了超级全局数组，但是在使用 $_SESSION 之前必须先使用 session_start() 函数来开启 Session 才行。

$_SESSION 为一个关联数组，和普通关联数组键值对的含义相同。设置 Session 的语法格式如下：

$_SESSION[名称] = 值;

【示例】使用 $_SESSION 定义 Session，并获取 Session 的值。

```

<?php
    session_start();
    $str = 'C 语言中文网';
    $arr = ['Session','$_SESSION'];
    $_SESSION['name']  = $str;
    $_SESSION['url']   = 'http://c.biancheng.net/php/';
    $_SESSION['title'] = $arr;

    foreach ($_SESSION as $key => $value) {
        if(is_array($value)){
            echo $key.'：';
            print_r($value);
        }else{
            echo $key.' = '.$value.'<br>';
        }
    }
?>
```

运行结果如下：

name = C 语言中文网
url = http://c.biancheng.net/php/
title：Array ( [0] => Session [1] => $_SESSION )

执行上面的脚本后，不仅会将这几个变量或数组保存到 $_SESSION 中，还会被保存到服务器端由“sess_”和 Session ID 所命名的文件中，这个文件的位置可以通过修改 php.ini 配置文件或使用 session.save_path 配置。而且这个文件可以直接使用文本编辑器打开，文件的内容结构如下所示：

变量名|类型:长度:值;    // Session 中的每个变量都使用相同的结构保存

执行上面的脚本所创建的 Session 文件的内容如下所示：

name|s:16:"C 语言中文网";url|s:27:"http://c.biancheng.net/php/";title|a:2:{i:0;s:7:"Session";i:1;s:9:"$_SESSION";}

如果想要修改设置好的 Session 的值，只需要像普通数组那样，重新为 Session 中指定的元素赋值即可，修改后的信息也会同步更新到对应的 Session 文件中。