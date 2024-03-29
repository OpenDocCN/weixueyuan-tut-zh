# PHP 变量作用域

> 原文：[`c.biancheng.net/view/7257.html`](http://c.biancheng.net/view/7257.html)

变量的作用范围（可以被使用的范围）称为变量作用域，变量必须在其有效范围内使用，如果超出有效范围，变量就会失去其意义。按作用域可以将 PHP 变量分为全局变量和局部变量两种。

可以在当前源码中任何地方（函数外部）使用的变量称为全局变量，它们具有全局作用域；只能在对其进行定义的函数内部使用的变量称为局部变量，它们具有局部作用域。

在 PHP 中，全局变量不能直接在函数内部使用；而局部变量也不能直接在函数外部使用。当然这也不是绝对的，通过一些方法还是可以实现在函数内调用全局变量、在函数外调用局部变量的，这些我们会在后面的学习中介绍。

## 局部变量与局部作用域

局部变量就是在函数的内部定义的变量，它只能在定义它的函数内使用。局部变量会在函数调用结束时自动销毁。

【示例】下面定义一个名为 example 的函数，并在函数内部定义一个局部变量 a，然后分别在该函数内部及函数外部尝试输出变量 a 的值，具体代码如下：

```

<?php
    function example(){
        $a = "C 语言中文网";  // 在函数内定义变量 a
        echo "局部变量 a 的值为：".$a."<br>";
    }
    example();
    if($a){                  // 在函数外部调用 $a,如果 $a 存在则会打印下面的内容
        echo "在函数外部调用函数内的局部变量 a，其值为：".$a;
    }
?>
```

运行结果如下：

局部变量 a 的值为：C 语言中文网

通过运行结果可以看出，在函数外部并不能调用函数内部定义的局部变量，因为局部变量 $a 的作用域是定义它的 example() 函数，在函数以外的地方无法使用。

在本例中只要了解局部变量的使用方法及意义即可，函数相关内容我们会在后面详细讲解。

## 全局变量与全局作用域

全局变量就是定义在所有函数以外的变量，其作用域是当前源码的任何地方，但是在函数内部是不可用的。在程序运行期间全局变量会一直存在，只有程序结束运行时才会被销毁。

【示例】定义一个全局变量 a，并分别在函数内部和外部输出全局变量的值，具体代码如下：

```

<?php
    $a = "C 语言中文网";      // 在函数外部定义全局变量 a
    function example(){
        if($a){
            echo "在函数内部调用全局变量 a，其值为：".$a;
        }
    }
    example();
    echo "在函数外部调用全局变量 a，其值为：".$a;
?>
```

运行结果如下：

在函数外部调用全局变量 a，其值为：C 语言中文网

通过运行结果可以看出，在函数内部并没有成功调用到 $a，但是在函数外部可以。