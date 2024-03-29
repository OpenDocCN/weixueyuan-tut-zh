# PHP 运算符

> 原文：[`c.biancheng.net/view/7268.html`](http://c.biancheng.net/view/7268.html)

运算符是指通过一个或多个表达式来产生另外一个值的某些符号，如“+”、“%”、“.”等都是运算符。

在表达式 2+1 中，运算符“+”有两个操作数，即 1 和 2。具有两个操作数的运算符可以称为双目运算符，具有一个操作数的运算符称为单目运算符。如表达式 -6，运算符“-”只有一个操作数 6，因此这里的“-”是单目运算符。

接下来为大家简单介绍一下 PHP 中常用的运算符。

## 算术运算符

算术运算（Arithmetic Operators）符是处理四则运算（加、减、乘、除四种运算）的符号，在数字的处理中应用得最多。常用的算术运算符如下表所示。

| 名称 | 操作符 | 举例 |
| 加法运算 | + | $a + $b |
| 减法运算 | - | $a - $b |
| 乘法运算 | * | $a * $b |
| 除法运算 | / | $a / $b |
| 取余数运算 | % | $a % $b |

注意：在算术运算符中使用 % 求余，如果被除数（$a）是负数，那么取得的结果也是一个负值。

【示例】分别使用上述几种算术运算符进行运算，代码如下所示：

```

<?php
    $a = -100;
    $b = 70;
    $c = 20;
    echo '$a = '.$a.', $b = '.$b.', $c = '.$c.'<br>';
    echo '$a + $b = '.($a + $b).'<br>';
    echo '$a - $b = '.($a - $b).'<br>';
    echo '$a * $c = '.($a * $c).'<br>';
    echo '$b / $c = '.($b / $c).'<br>';
    echo '$b % $c = '.($b % $c).'<br>';
?>
```

运行结果如下：

$a = -100, $b = 70, $c = 20
$a + $b = -30
$a - $b = -170
$a * $c = -2000
$b / $c = 3.5
$b % $c = 10

## 字符串运算符

字符串运算符只有一个，即英文的句号“.”，它可以将两个字符串连接起来，拼接成一个新的字符串。使用过 C 或 Java 语言的读者应注意，PHP 里的“+”只能用作赋值运算符，而不能用作字符串运算符。

【示例】使用字符串运算符“.”拼接两个字符串，代码如下所示：

```

<?php
    $str1 = 'C 语言';
    $str2 = '中文网';
    $str3 = $str1.$str2;
    echo $str3;
?>
```

运行结果如下：

C 语言中文网

## 赋值运算符

最基本的赋值运算符是“=”，用于对变量进行赋值操作，而其他运算符可以和赋值运算符“=”联合使用，构成组合赋值运算符。赋值运算符是把基本赋值运算符“=”右边的值赋给左边的变量。

在 PHP 中的赋值运算符如下表所示：

| 操作 | 符号 | 举例 | 展开形式 | 意义 |
| 赋值 | = | $a = 2 | $a = 2 | 将右边表达式的值赋给左边的变量 |
| 加 | += | $a += 2 | $a = $a + 2 | 将运算符左边的变量加上右边表达式的值赋给左边的变量 |
| 减 | -= | $a -= 2 | $a = $a - 2 | 将运算符左边的变量减去右边表达式的值赋给左边的交量 |
| 乘 | *= | $a *= 2 | $a = $a * 2 | 将运算符左边的变量乘以右边表达式的值赋给左边的变量 |
| 除 | /= | $a /= 2 | $a = $a / 2 | 将运算符左边的变量除以右边表达式的值赋给左边的变量 |
| 拼接字符 | .= | $a .= '2' | $a = $a . '2' | 将右边的字符追加到左边 |
| 取余数 | %= | $a %= 2 | $a = $a % 2 | 将运算符左边的变量用右边表达式的值求模，并将结果赋给左边的变量 |

【示例】应用赋值运算符给指定变量赋值，并计算结果，代码如下所示：

```

<?php
    $a = 9;
    $b = 6;
    echo '$a = '.$a.', $b = '.$b.'<br>';
    echo '$a += $b 的值为：'.($a += $b).'<br>';
    echo '$a -= $b 的值为：'.($a -= $b).'<br>';
    echo '$a *= $b 的值为：'.($a *= $b).'<br>';
    echo '$a /= $b 的值为：'.($a /= $b).'<br>';
?>
```

运行结果如下：

$a = 9, $b = 6
$a += $b 的值为：15
$a -= $b 的值为：9
$a *= $b 的值为：54
$a /= $b 的值为：9

## 位运算符

位运算符是指将二进制位从低位到高位对齐后进行运算。在 PHP 中的位运算符如下表所示。

| 符号 | 作用 | 举例 |
| & | 按位与 | $m & $n |
| &#124; | 按位或 | $m &#124; $n |
| ^ | 按位异或 | $m ^ $n |
| ~ | 按位取反 | ~$m |
| <<  | 向左移位 | $m << $n |
| >>  | 向右移位 | $m >> $n |

【示例】使用位运算符对变量中的值进行位运算操作，代码如下所示：

```

<?php
    $m = 8;
    $n = 12;
    echo '$m = '.$m.', $n = '.$n.'<br>';
    echo '$m & $n = '.($m & $n).'<br>';
    echo '$m | $n = '.($m | $n).'<br>';
    echo '$m ^ $n = '.($m ^ $n).'<br>';
    echo '~ $m = '.(~$m).'<br>';
    echo '$m << $n = '.($m << $n).'<br>';
    echo '$m >> $n = '.($m >> $n);
?>
```

运行结果如下：

$m = 8, $n = 12
$m & $n = 8
$m | $n = 12
$m ^ $n = 4
~ $m = -9
$m << $n = 32768
$m >> $n = 0

## 递增和递减运算符

算术运算符适合在有两个或者两个以上不同操作数的场合使用，但是当只有一个操作数时，使用算术运算符是没有必要了。这时就可以使用递增“++”或递减“--”运算符了。

递增或递减运算符有两种使用方法：

*   一种是先将变量增加或者减少 1 后再将值赋给原变量，称为前置递增或递减运算符（也称前置自增自减运算符）；
*   一种是将运算符放在变量后面，即先返回变量的当前值，然后再将变量的值增加或者减少 1，称为后置递增或递减运算符（后置自增自减运算符）。

注意：递增和递减操作符与变量之间不能有空格。

【示例】下面通过简单的示例来演示递增和递减运算符的使用，代码如下所示：

```

<?php
    $a = 5;
    $b = 3;
    $c = 8;
    $d = 11;
    echo '$a = '.$a.', $b = '.$b.', $c = '.$c.', $d = '.$d.'<br>';
    echo '++$a = '.++$a.'<br>';
    echo '$b++ = '.$b++.'<br>';
    echo '--$c = '.--$c.'<br>';
    echo '$d-- = '.$d--.'<br>';
?>
```

运行结果如下：

$a = 5, $b = 3, $c = 8, $d = 11
++$a = 6
$b++ = 3
--$c = 7
$d-- = 11