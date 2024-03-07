# PHP 变量详解

> 原文：[`c.biancheng.net/view/6117.html`](http://c.biancheng.net/view/6117.html)

变量是任何程序设计语言中一个基本而且重要的概念。本节我们就来带领大家认识一下 PHP 中的变量。

## 什么是变量

程序中的变量源于数学，在程序语言中能够储存结果或者表示抽象概念。简单理解变量就是临时存储值的容器，它可以储存数字、文本、或者一些复杂的数据等。

变量在 PHP 中居于核心地位，是使用 PHP 的关键所在，变量的值在程序运行中会随时发生变化，能够为程序中准备使用的一段数据起一个简短容易记的名字，另外它还可以保存用户输入的数据或运算的结果。

## 声明（创建）变量

因为 PHP 是一种弱类型的语言，所以使用变量前不用提前声明，变量在第一次赋值时会被自动创建，这个原因使得 PHP 的语法和 C 语言、Java 等强类型语言有很大的不同。

声明 PHP 变量必须使用一个美元符号“$”后面跟变量名来表示，然后再使用“=”给这个变量赋值。如下所示：

```

<?php
    $a = 1;
    $b = 2;
    $c = 3;
    echo $a.', '.$b.', '.$c;
?>
```

运行结果如下：

1, 2, 3

## 变量的命名规则

变量名并不是可以随意定义的，一个有效的变量名应该满足以下几点要求：

*   变量必须以 $ 符号开头，其后是变量的名称，$ 并不是变量名的一部分；
*   变量名必须以字母或下划线开头；
*   变量名不能以数字开头；
*   变量名只能包含字母（A~z）、数字（0~9）和下划线（_）；
*   与其它语言不通的是，PHP 中的一些关键字也可以作为变量名（例如 $true、$for）。

注意：PHP 中的变量名是区分大小写的，因此 $var 和 $Var 表示的是两个不同的变量。

正确的变量命名如下所示：

```

<?php
    $name = "C 语言中文网";                  // 变量名为：name，变量值为：C 语言中文网
    $url = "http://c.biancheng.net/";       // 变量名为：url，变量值为：http://c.biancheng.net/
    $Url = "http://c.biancheng.net/php/";   // 变量名为：Url，变量值为：http://c.biancheng.net/php/
    $_str = "PHP 是世界上最好的语言";        // 变量名为：_str，变量值为：PHP 是世界上最好的语言
    echo $name.'<br>'.$url.'<br>'.$Url.'<br>'.$_str;
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/
http://c.biancheng.net/php/
PHP 是世界上最好的语言

错误的变量命名如下所示：

```

<?php
    $1122_num = 11111;        // 变量名不能以数字开头
    $~%_str = "PHP 教程";      // 变量名不能包含特殊字符
?>
```

运行上面的代码会提示语法错误。

注意：虽然以字母和下划线开头，后面跟随中文字符也可以作为变量名称，但是并不建议这么做。

虽然只要满足上面的命名规则，变量名就是有效的，但是还是建议定义的变量名要有一定的意义，例如使用 name 表示姓名；使用 url 表示网站链接等。

当使用多个单词构成变量名时，可以使用下面的命名规范：

*   下划线命名法：将构成变量名的单词以下划线分割，例如 $get_user_name、$set_user_name；
*   驼峰式命名法(推荐使用)：第一个单词全小写，后面的单词首字母小写，例如 $getUserName、$getDbInstance；
*   帕斯卡命名法：将构成变量名的所有单词首字母大写，例如 $Name、$MyName、$GetName。