# PHP 声明参数类型

> 原文：[`c.biancheng.net/view/6152.html`](http://c.biancheng.net/view/6152.html)

在 PHP 5 中已引入函数的参数类型声明，如果给定的值不是一个合法的参数类型，那么在 PHP 5 中会出现一个 Fatal error，在 PHP 7 中则会抛出一个 TypeError exception。

在 PHP 7 中增加了参数可声明的类型种类，如表所示。

参数可声明的类型

| 类型 | 说明 | PHP 版本 |
| class/interface name（类，接口） | 参数必须是指定类或接口的实例 | PHP 5.0.0 |
| Array | 参数为数组类型 | PHP 5.1.0 |
| Callable | 参数为有效的回调类型 | PHP 5.4.0 |
| Bool | 参数为布尔型 | PHP 7.0.0 |
| Float | 参数为浮点型 | PHP 7.0.0 |
| Int | 参数为整型 | PHP 7.0.0 |
| String | 参数为字符串 | PHP 7.0.0 |
| class/interface name（类，接口） | 参数必须是指定类或接口的实例 | PHP 5.0.0 |
| Array | 参数为数组类型 | PHP 5.1.0 |

指定参数类型为 class 类型的实例如下：

```

<?php
class C{}
class D extends C{}      //类 D 继承自类 C
class E{}
functionf(C $c){
    echo get_class($c)."\n";
}
f(new C);
f(new D);
f(new E);
?>
```

执行以上程序的结果是：

C D
Fatal error: Uncaught TypeError: Argument 1 passed to f() must be an instance of C, instance of E given, called in /Library/WebServer/Documents/book/str.php on line 293 and defined in /Library/WebServer/Documents/book/str.php:287 Stack trace: #0 /Library/WebServer/Documents/book/str.php(293): f(Object(E)) #1 {main} thrown in /Library/WebServer/Documents/book/str.php on line 287

## 类型转换

默认情况下，当传递的参数不是函数指定的参数类型时，PHP 会尝试将所传参数转换成指定参数类型。例如，一个函数希望得到一个字符串类型的参数，但假如给其提供的是一个整型参数，PHP 就会自动将其转换成字符串类型，或者一个函数希望得到一个整型参数，但却给它传递了一个浮点型的参数。

示例如下：

```

<?php
function test(int $a, string $b, string $c){
    echo ($a + $b);
    echo " the string is $c";
}
test(3.8,2,'hello');
?>
```

执行以上代码的打印结果为：

5 the string is hello

注意，在将浮点型转成整型时，只取其中的整数部分。

## 严格模式

在 PHP 7 中，可以使用`declare(strict_types=1)`设置严格模式，这样只有在传递的参数与函数期望得到的参数类型一致时才能正确执行，否则会抛出错误。只有一种情况例外，就是当函数期望得到的是一个浮点型数据而提供的是整型时，函数也能正常被调用。

请看如下示例：

```

<?php
declare(strict_types=1);
function test(int $a, int $b, string $c){
    echo ($a + $b);
    echo " the string is $c";
}
test(3.8,2,'hello');
?>
```

此处 declare 声明了 PHP 为严格模式，而传入的参数与函数期望得到的参数类型不一致，所以会报错，如下所示：

Fatal error: Uncaught TypeError: Argument 1 passed to test() must be of the type integer, float given, called in /Library/WebServer/Documents/book/str.php on line 285 and defined in /Library/WebServer/Documents/book/str.php:281 Stack trace: #0 /Library/WebServer/Documents/book/str.php(285): test(3.8, 2, 'hello') #1 {main} thrown in /Library/WebServer/Documents/book/str.php on line 281