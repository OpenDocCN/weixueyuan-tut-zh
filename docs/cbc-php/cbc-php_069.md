# PHP 二维数组和多维数组

> 原文：[`c.biancheng.net/view/6154.html`](http://c.biancheng.net/view/6154.html)

通过前面的学习我们知道，PHP 中的数组可以存储所有类型的数据，当然也包括数组本身。如果一个数组中的元素是另一个数组，就构成了包含数组的数组，即二维数组。

除了二维数组外，PHP 还支持三维数组、四维数组甚至更加多维度的数组，这里可以统称为多维数组（multidimensional array），不过数组超过三维之后可读性就会大大降低，同时也不便于管理。

## 1\. 二维数组

二维数组和一维数组的声明方式一样，只是将数组中的一个或多个元素也声明成一个数组，同时也有直接为数组元素赋值和使用 array() 函数两种声明二维数组的方法。

下面使用直接为数组元素赋值的方式来声明一个数组，示例代码如下：

```

<?php
    $array[0]['name'] = 'zhangsan';
    $array[0]['chinese'] = '89';
    $array[0]['math'] = '95';
    $array[0]['english'] = '88';
    $array[1]['name'] = 'lisi';
    $array[1]['chinese'] = '91';
    $array[1]['math'] = '86';
    $array[1]['english'] = '90';
    echo '<pre>';
    print_r($array);
?>
```

运行结果如下：

Array
(
    [0] => Array
        (
            [name] => zhangsan
            [chinese] => 89
            [math] => 95
            [english] => 88
        )
    [1] => Array
        (
            [name] => lisi
            [chinese] => 91
            [math] => 86
            [english] => 90
        )
)

使用 array() 函数声明二维数组与声明一维数组相似，示例代码如下：（以下与上面代码是等价的，运行结果是相同的）

```

<?php
    $array = array(
        array('name'=>'zhangsan','chinese'=>'89','math'=>'95','english'=>'88'),
        array('name'=>'lisi','chinese'=>'91','math'=>'86','english'=>'90')
    );
    echo '<pre>';
    print_r($array);
?>
```

同样的，获取二维数组中的元素也和一维数组类似，只需要注明每个维度的下标即可，示例代码如下：

```

<?php
    $array = array(
        array('name'=>'zhangsan','chinese'=>'89','math'=>'95','english'=>'88'),
        array('name'=>'lisi','chinese'=>'91','math'=>'86','english'=>'90')
    );
    echo $array[0]['name'].'同学的数学考了'.$array[0]['math'].'分';
?>
```

运行结果如下：

zhangsan 同学的数学考了 95 分

提示：数组的不同维度标志着我们需要使用几个下标（索引）来获取对应的数组元素，比如二维数组需要使用两个下标来获取对应的数组元素，三维数组则需要三个，以此类推。

## 2\. 多维数组

参考二维数组，举一反三，可以很容易地创建三维数组、四维数组或者其他更高维数的数组。定义一个三维数组的示例如下：

```

<?php
    $arr = array(
        '安徽' => array(
            '阜阳'=>array('阜南县','临泉县','颍州区'),
            '宿州'=>array('墉桥区','灵璧县','泗县'),
            '合肥'=>array('蜀山区','长丰县','肥东')
        ),
        '河南' => array(
            '洛阳'=>array('西工区','老城区','孟津县'),
            '郑州市'=>array('中原区','金水区')
        )
    );
    print_r($arr);
    echo $arr['安徽']['宿州'][0];  // 输出墉桥区
?>
```

其中“安徽”对应的是一个二维数组，“阜阳”“宿州”“合肥”分别对应一个一维数组；同理，“河南”也对应一个二维数组。“安徽”和“河南”分别对应一个二维数组，它俩组合起来形成一个三维数组。 PHP 中对多维数组没有上限的固定限制，但是随着维数的增加，数组会越来越复杂，对于阅读调试和维护都会稍微困难些。 以上程序的运行结果为：

Array
(
    [安徽] => Array
        (
            [阜阳] => Array
                (
                    [0] => 阜南县
                    [1] => 临泉县
                    [2] => 颍州区
                )
            [宿州] => Array
                (
                    [0] => 墉桥区
                    [1] => 灵璧县
                    [2] => 泗县
                )
            [合肥] => Array
                (
                    [0] => 蜀山区
                    [1] => 长丰县
                    [2] => 肥东
                )
        )
    [河南] => Array
        (
            [洛阳] => Array
                (
                    [0] => 西工区
                    [1] => 老城区
                    [2] => 孟津县
                )
            [郑州市] => Array
                (
                    [0] => 中原区
                    [1] => 金水区
                )
        )
)
墉桥区