# PHP define()和 const：定义常量

> 原文：[`c.biancheng.net/view/6118.html`](http://c.biancheng.net/view/6118.html)

和变量相对应的是常量，本节我们就来学习一下 PHP 中的常量。

## 什么是常量

所谓常量就是不能改变的量，PHP 中常量一旦被定义，就不能被修改或取消定义。

PHP 常量通常用来存储一个不被改变也不希望变化的数据，该数据只能是四种标量数据类型的数据：整型、浮点型、字符串、布尔型，不过从 PHP7 开始常量支持了数组（array）类型。

与变量相比，常量具有以下几个特点：

*   常量前面没有美元符号（$）；
*   常量只能用 define() 和 const 定义；
*   常量的作用域是全局的；
*   常量一旦被定义就不能被重新定义或者取消定义。

## 定义常量

合法的常量名只能以字母和下划线开始，后面可以跟着任意长度的字母、数字或下划线。在 PHP 中可以通过 define() 函数定义一个常量，define() 函数的语法如下：

define(string $name, mixed $value [, bool $case_insensitive = false])

define() 函数参数的说明如下表所示：

| 参数 | 说明 |
| $name | 必选参数，常量名称（需要使用双引号或单引号包裹） |
| $value | 必选参数，常量的值 |
| $case_insensitive | 可选参数，指定是否大小写敏感，默认是大小写敏感的，设定为 true 则表示大小写不敏感 |

除了可以使用 define() 函数之外，还可以使用 const 关键字来定义常量，const 关键字的语法如下：

const 常量名 = 常量值;

下面通过示例来演示一下常量的使用：

```

<?php
    define('WebSite', 'C 语言中文网');
    const url = 'http://c.biancheng.net/php/';
    echo WebSite.'<br>';
    echo url;
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/

## 获取常量的值

想要获取常量的值，除了可以直接使用常量名外，还可以使用 constant() 函数，使用函数和直接使用常量名的效果是一样的。但使用函数可以动态输出不同的常量，在使用上要灵活、方便得多，constant() 函数的语法格式为：

constant(string $name);

其中 $name 为要获取的常量名称，也可以是存储常量名的变量。如果成功则返回常量的值；如果常量未定义则返回一个 E_WARNING 级别的错误。示例代码如下：

```

<?php
    define('WebSite', 'C 语言中文网');
    const url = 'http://c.biancheng.net/php/';
    $website = 'WebSite';
    $url = 'url';
    echo constant($website).'<br>';
    echo constant($url);
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/

## 判断常量是否存在

要判断一个常量是否已经定义，可以使用 defined() 函数。函数的语法格式如下：

defined(string $name)

其中 $name 为待判断的常量名称，函数会返回一个 bool 类型的值，常量已定义则返回 true，未定义则返回 false。示例代码如下：

```

<?php
    define('WebSite', 'C 语言中文网');
    const url = 'http://c.biancheng.net/php/';
    var_dump(defined('WebSite'));
    echo '<br>';
    var_dump(defined('url'));
?>
```

运行结果如下：

bool(true)
bool(true)