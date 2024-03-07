# PHP __destruct()：析构函数/方法

> 原文：[`c.biancheng.net/view/7504.html`](http://c.biancheng.net/view/7504.html)

前面我们介绍了 PHP 的构造函数《__construct()》，它可以在对象被创建时自动调用。在 PHP5 中还提供了一个与之相对应的函数——析构函数。

析构函数的作用和构造函数正好相反，析构函数只有在对象被垃圾收集器收集前（即对象从内存中删除之前）才会被自动调用。析构函数允许我们在销毁一个对象之前执行一些特定的操作，例如关闭文件、释放结果集等。

在 PHP 中有一种垃圾回收机制，当对象不能被访问时就会自动启动垃圾回收机制，收回对象占用的内存空间。而析构函数正是在垃圾回收机制回收对象之前调用的。

析构函数的声明格式与构造函数相似，在类中声明析构函数的名称也是固定的，同样以两个下画线开头的方法名`__destruct()`，而且析构函数不能带有任何参数。在类中声明析构方法的格式如下：

public function __destruct(){
    ... ...
}

在 PHP 中析构函数并不是很常用，它属于类中可选的一部分，只有需要的时候才在类中声明。

【示例】下面通过示例来演示一下析构函数的使用。

```

<?php
    class Website{
        public $name, $url, $title;
        public function __construct(){
            echo '------这里是构造函数------<br>';
        }
        public function __destruct(){
            echo '------这里是析构函数------<br>';
        }
    }

    $object = new Website();
    echo 'C 语言中文网<br>';
    echo 'http://c.biancheng.net/php/<br>';
    echo '脚本运行结束之前会调用对象的析构函数<br>';
?>
```

运行结果如下：

------这里是构造函数------
C 语言中文网
http://c.biancheng.net/php/
脚本运行结束之前会调用对象的析构函数
------这里是析构函数------