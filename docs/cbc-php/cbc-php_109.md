# PHP instanceof：判断对象是否属于某个类

> 原文：[`c.biancheng.net/view/6236.html`](http://c.biancheng.net/view/6236.html)

使用 PHP 中的 instanceof 运算符，可以判断一个对象是否属于某一个类，语法格式如下：

对象名 instanceof 类名;

【示例】使用 instanceof 判断一个对象是否属于某个类。

```

<?php
    class A{
    }
    class B{
    }
    $obj = new A;

    var_dump($obj instanceof A);
    echo '<br>';
    var_dump($obj instanceof B);
?>
```

运行结果如下：

bool(true)
bool(false)

另外，使用 instanceof 也可用来确定一个对象是不是继承自某个父类的子类，示例代码如下：

```

<?php
    class A{
    }
    class B extends A{
    }
    $obj = new B;

    var_dump($obj instanceof A);
    echo '<br>';
    var_dump($obj instanceof B);
?>
```

运行结果如下：

bool(true)
bool(true)

最后，instanceof 也可用于确定一个变量是不是实现了某个接口的对象的实例，示例代码如下：

```

<?php
    interface A{
    }
    class B implements A{
    }
    $obj = new B;

    var_dump($obj instanceof A);
    echo '<br>';
    var_dump($obj instanceof B);
?>
```

运行结果如下：

bool(true)
bool(true)

虽然 instanceof 通常是直接与类名一起使用，但也可以使用字符串来代替，示例代码如下：

```

<?php
    interface A{
    }
    class B implements A{
    }
    $obj = new B;
    $str1 = 'A';
    $str2 = 'B';

    var_dump($obj instanceof A);
    echo '<br>';
    var_dump($obj instanceof B);
    echo '<br>';
    var_dump($obj instanceof $str1);
    echo '<br>';
    var_dump($obj instanceof $str2);
?>
```

运行结果如下：

bool(true)
bool(true)
bool(true)
bool(true)

注意：使用字符串代替类名时，需要将字符串赋值给一个变量，直接使用字符串（例如 $obj instanceof 'A'）是不行的。

如果被检测的变量不是对象，instanceof 并不会报错而是直接返回 FALSE。另外，不能使用 instanceof 来检测常量。示例代码如下：

```

<?php
    $a = 1;
    $b = NULL;
    $c = imagecreate(5, 5);
    var_dump($a instanceof stdClass);
    echo '<br>';
    var_dump($b instanceof stdClass);
    echo '<br>';
    var_dump($c instanceof stdClass);
    echo '<br>';
    var_dump(FALSE instanceof stdClass);
?>
```

运行结果如下：

bool(false)
bool(false)
bool(false)
bool(false)