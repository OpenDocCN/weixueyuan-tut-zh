# PHP foreach 循环

> 原文：[`c.biancheng.net/view/6128.html`](http://c.biancheng.net/view/6128.html)

PHP 中提供了 4 种循环控制语句，分别是 while、do while、for 和 foreach。本节只讲解 foreach 循环，其它 3 种循环请猛击以下链接学习：

*   PHP while 和 do while 循环
*   PHP for 循环

PHP foreach 循环结构是遍历数组时常用的方法，foreach 仅能够应用于数组和对象，如果尝试应用于其他数据类型的变量或者未初始化的变量将发出错误信息。

foreach 有以下两种语法格式：

//格式 1
foreach (array_expression as $value){
    statement
}

//格式 2
foreach (array_expression as $key => $value){
    statement
}

第一种格式遍历 array_expression 数组时，每次循环将数组的值赋给 $value；第二种遍历不仅将数组值赋给 $value，还将键名赋给 $key。

举例演示两种格式的不同：

```

<?php
$array = [0, 1, 2];
foreach ($array as $val){
    echo "值是：" . $val ;
    echo "<br/>";
}
foreach ($array as $key => $value) {
    echo "键名是：" . $key . "值是：" . $value;
    echo "<br/>";
}
?>
```

执行以上代码打印的结果是：

```

值是：0
值是：1
值是：2
键名是：0 值是：0
键名是：1 值是：1
键名是：2 值是：2
```

## foreach 在 PHP5 和 PHP7 中的区别

在 PHP 5 版本中，当 foreach 开始循环执行时，每次数组内部指针都会自动向后移动一个单元，但是在 PHP 7 中却不是这样。如下代码在 PHP 5 和 PHP 7 中的执行结果会有所不同。

```

<?php
$array = [0, 1, 2];
foreach ($array as $val){
    var_dump(current($array));
}
?>
```

在 PHP 5 中的输出结果为：

int(0) int(1) int(2)

但在 PHP 7 中的输出结果却为：

int(0) int(0) int(0)

在 PHP 7 中，按照值进行循环时，foreach 是对数组的复制操作，在循环过程中对数组的修改不会影响循环行为，但在 PHP 5 中却会有影响。

```

<?php
$array = [0, 1, 2];
//$ref =& $array; // Necessary to trigger the old behavior
foreach ($array as $val) {
    var_dump($val);
    unset($array[1]);
}
?>  
```

在 PHP 7 中的输出结果为：

int(0) int(1) int(2)

但在 PHP 5 中的输出结果却为：

int(0) int(2)

在 PHP 7 中按照引用循环的时候对数组的修改会影响循环，在 PHP 5 中 则不会改变。示例如下：

```

<?php
$array = [0];
foreach ($array as &$val) {
    var_dump($val);
    $array[1] = 1;
    $array[2] = 2;
}
?>
```

在 PHP 7 中的运行结果是：

int(0) int(1) int(2)

但在 PHP 5 中的运行结果却是：

int(0)