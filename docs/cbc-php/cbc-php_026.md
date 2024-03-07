# PHP 逻辑运算符

> 原文：[`c.biancheng.net/view/7269.html`](http://c.biancheng.net/view/7269.html)

逻辑运算符用来组合逻辑运算的结果，是程序设计中一组非常重要的运算符。

PHP 中的逻辑运算符如下表所示：

| 逻辑运算符 | 示例 | 何时结果为真 |
| && 或 and（逻辑与） | $m && $n 或 $m and $n | 当 $m 和 $n 都为真时结果为真，$m 和 $n 有任意一个为假时结果为假 |
| &#124;&#124; 或 or（逻辑或） | $m &#124;&#124; $n 或 $m or $n | 只要 $m 和 $n 中有任意一个为真，则结果为真 |
| xor（逻辑异或） | $m xor $n | 当 $m 和 $n 一真一假时，结果为真 |
| !（逻辑非） | !$m | 当 $m 为假时，结果为真 |

## “&&”或“and” 逻辑与

左右两个条件同时为 TRUE 时，则结果为 TRUE；两个条件中有任意一个条件为 FALSE 时，则结果为 FALSE。当左边的条件为 FALSE 时，将直接跳过右边的条件直接返回 FALSE。

【示例】假设 18 岁到 25 岁之间符合征兵的条件，小明今年 21 岁，判断小明适不适合当兵，实现代码如下：

```

<?php
    $age = 21;
    if($age >= 18 && $age <= 25){
        echo '符合当兵条件！';
    }else{
        echo '不符合当兵条件！';
    }
?>
```

示例中我们使用到的 if else 语句会在后面详细讲解。

运行结果如下：

符合当兵条件！

## “||”或“or” 逻辑或

如果两个条件有一个为 TRUE，则结果为 TRUE；如果两个条件都是为 FALSE，则结果为 FALSE。当左边条件为 TRUE，将跳过右边条件的判断，直接返回 TRUE。

【示例】调整上面的示例，使用逻辑或运算符实现，代码如下所示：

```

<?php
    $age = 21;
    if($age < 18 || $age > 25){
        echo '不符合当兵条件！';
    }else{
        echo '符合当兵条件！';
    }
?>
```

## “!” 逻辑非

将一个布尔值进行取反操作。例如：!true = false、!false = true、!10 = false。

【示例】同样使用前面的示例，使用逻辑非操作符实现，代码如下所示：

```

<?php
    $age = 21;
    if(!($age < 18 || $age > 25)){
        echo '符合当兵条件！';
    }else{
        echo '不符合当兵条件！';
    }
?>
```

【示例】判断指定的年份是不是闰年：

```

<?php
    $year = 2020;
    if($year%4==0 && $year%100!=0 || $year%400==0){
        echo $year.'年是闰年！';
    }else{
        echo $year.'不年是闰年！';
    }
?>
```

运行结果如下：

2020 年是闰年！