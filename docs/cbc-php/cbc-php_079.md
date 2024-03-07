# PHP foreach 遍历数组

> 原文：[`c.biancheng.net/view/7281.html`](http://c.biancheng.net/view/7281.html)

在 PHP 中，很少需要自己动手将大量的数据声明在数组中，而是通过调用函数返回值或者数据库查询结果来获取一个数组。使用数组的目的，就是将多个相互关联的数据，组织在一起形成集合，作为一个单元使用，达到批量处理数据的目。大部分数组都需要使用遍历的方式来处理数组中的每个元素。

foreach 是 PHP 中专门为遍历数组而设计的语句，和 Perl 及其他语言很像，是一种遍历数组的简便方法。

使用 foreach 语句遍历数组时与数组的下标无关，不管是不是连续的索引数组，还是以字符串为下标的关联数组，都可以使用 foreach 语句遍历。foreach 只能应用于数组，自 PHP5 起，还可以遍历对象。

foreach 语句有两种语法格式，第二种比较次要但却是第一种有用的扩展。

//第一种格式
foreach (array_expression as $value){
    statement
}
//第二种格式
foreach (array_expression as $key => $value){
    statement
}

第一种格式会循环遍历给定的 array_expression 数组。每次循环中，当前数组元素的值被赋给 $value，并且数组内部的指针向前移一步（因此下一次循环中将会得到下一个数组元素），直到遍历到数组的末尾，停止遍历并退出循环。

第二种格式功能与第一种相同，只是除了当前数组元素的键名也会在每次循环中被赋给变量 $key。

提示：$key 和 $value 的名称不是固定的，使用时我们可以根据喜好自行定义。

示例代码如下所示：

```

<?php
    $array = array('C 语言中文网','PHP 教程','PHP 数组','http://c.biancheng.net/php/');
    $num = 0;
    foreach ($array as $value) {
       echo '数组第'.$num.'个元素的值是：'.$value.'<br>';
       $num++;
    }
?>
```

上面代码使用的是 foreach 的第一种格式，如果使用第二种格式的换就可以省略掉变量 $num，代码如下所示：

```

<?php
    $array = array('C 语言中文网','PHP 教程','PHP 数组','http://c.biancheng.net/php/');

    foreach ($array as $key => $value) {
       echo '数组第'.$key.'个元素的值是：'.$value.'<br>';
    }
?>
```

这两组代码的运行结果是相同的，如下所示：

数组第 0 个元素的值是：C 语言中文网
数组第 1 个元素的值是：PHP 教程
数组第 2 个元素的值是：PHP 数组
数组第 3 个元素的值是：http://c.biancheng.net/php/

foreach 和其它循环语句（如 for 和 while）一样可以嵌套使用，当我们遍历多维数组时，就需要将 foreach 语句嵌套起来使用。示例代码如下：

```

<?php
    $array = array(
        array('name'=>'zhangsan','chinese'=>'89','math'=>'95','english'=>'88'),
        array('name'=>'lisi','chinese'=>'91','math'=>'86','english'=>'90')
    );
    foreach ($array as $key => $value) {
        echo '二维数组中第'.$key.'个数组的遍历结果：<br>';
        if(is_array($value)){
            foreach ($value as $k => $v) {
                echo '&nbsp;&nbsp;&nbsp;&nbsp;数组的键为：'.$k.'，值为：'.$v.'<br>';
            }
        }
    }
?>
```

运行结果如下：

二维数组中第 0 个数组的遍历结果：
    数组的键为：name，值为：zhangsan
    数组的键为：chinese，值为：89
    数组的键为：math，值为：95
    数组的键为：english，值为：88
二维数组中第 1 个数组的遍历结果：
    数组的键为：name，值为：lisi
    数组的键为：chinese，值为：91
    数组的键为：math，值为：86
    数组的键为：english，值为：90