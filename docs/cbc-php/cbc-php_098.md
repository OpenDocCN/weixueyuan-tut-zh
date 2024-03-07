# PHP __construct()：构造函数/方法

> 原文：[`c.biancheng.net/view/7501.html`](http://c.biancheng.net/view/7501.html)

构造函数（constructor method，也称为构造器）是类中的一种特殊函数，当使用 new 关键字实例化一个对象时，构造函数将会自动调用。

在 PHP3.0 和 PHP4.0 中，构造函数是一个与其所在类同名的函数。而在 PHP5 中，虽然也支持 PHP3.0 和 PHP4.0 中的用法，但是更推荐使用`__construct`作为类的构造函数，这样做的好处就是构造函数无需随着类名的改变而做出修改。在 PHP7.0 中废弃了 PHP3.0 和 PHP4.0 中的用法，构造函数必须使用 `__construct` 来定义。

构造函数就是当对象被创建时，类中被自动调用的第一个函数，并且一个类中只能存在一个构造函数。和普通函数类似构造函数也可以带有参数，如果构造函数有参数的话，那么在实例化也需要传入对应的参数，例如 `new Students($name, $age)`。

创建构造函数的语法格式如下：

public function __construct(参数列表){
    ... ...
}

其中，参数列表是可选的，不需要时可以省略。

如果没有在代码中显式地声明构造函数，类中会默认存在一个没有参数列表并且内容为空的构造函数。如果显式地声明构造函数则类中的默认构造方法将不会存在。所以构造函数通常用来做一些准备工作，比如为某些参数赋值等。

注意：如果显式地声明构造函数，那么它的访问权限必须是 public，而且构造函数是在实例化时自动调用的，我们不需要手动调用。

【示例】创建一个类，并为其显示的创建构造函数，代码如下：

```

<?php
    class Website{
        public $name, $url, $title;
        public function __construct($str1, $str2, $str3){
            $this -> name  = $str1;
            $this -> url   = $str2;
            $this -> title = $str3;
            $this -> demo();
        }
        public function demo(){
            echo $this -> name.'<br>';
            echo $this -> url.'<br>';
            echo $this -> title.'<br>';
        }
    }

    $object = new Website('C 语言中文网','http://c.biancheng.net/php/','构造函数');
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
构造函数

代码中我们用到了 $this，它表示当前调用的对象，而且 $this 只能在类的方法中使用，更多关于 $this 的知识后面我们会详细介绍。