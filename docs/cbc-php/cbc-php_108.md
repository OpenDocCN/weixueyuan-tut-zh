# PHP clone 关键字（克隆对象）

> 原文：[`c.biancheng.net/view/6235.html`](http://c.biancheng.net/view/6235.html)

PHP 中的对象模型是通过引用来调用对象的，但有时需要建立一个对象的副本，在改变原有对象时不希望影响到对象副本。如果使用`new`关键字重新创建对象，再为属性赋上相同的值，这样做会比较烦琐而且也容易出错。在 PHP 中可以根据现有的对象克隆出一个完全一样的对象，克隆以后，原本对象和副本对象是完全独立互不干扰的。

在 PHP 中可以使用 clone 关键字克隆对象，语法格式如下：

克隆对象名称 = clone 原对象名称;

因为 clone 的方式实际上是对整个对象的内存区域进行了一次复制并用新的对象变量指向新的内存，因此赋值后的对象和原对象之间是相互独立的。

对象克隆成功后，它们中的成员方法、属性以及值是完全相同的。如果要对克隆后副本的成员属性重新赋值，可以使用《PHP 魔术方法》中介绍的 __clone() 方法。

【示例】下面通过一个简单的示例来演示一下 clone 关键字的使用。

```

<?php
    class Website{
        public $name, $url;
        public function __construct($name, $url){
            $this -> name = $name;
            $this -> url  = $url;
        }
        public function output(){
            echo $this -> name.'，'.$this -> url.'<br>';
        }
    }

    $obj  = new Website('C 语言中文网', 'http://c.biancheng.net/php/');
    $obj2 = clone $obj;
    $obj  -> output();
    $obj2 -> output();
    echo '<pre>';
    var_dump($obj);
    var_dump($obj2);
?>
```

运行结果如下：

C 语言中文网，http://c.biancheng.net/php/
C 语言中文网，http://c.biancheng.net/php/
object(Website)#1 (2) {
  ["name"]=>
  string(16) "C 语言中文网"
  ["url"]=>
  string(27) "http://c.biancheng.net/php/"
}
object(Website)#2 (2) {
  ["name"]=>
  string(16) "C 语言中文网"
  ["url"]=>
  string(27) "http://c.biancheng.net/php/"
}

注意：如果使用 `=` 将一个对象赋值给一个变量，那么这时得到的将是一个对象的引用，通过这个变量更改属性的值将会影响原来的对象。

## __clone() 魔术方法

__clone() 方法不能够直接被调用，只有当通过 clone 关键字克隆一个对象时才可以使用该对象调用 __clone() 方法。当创建对象的副本时，PHP 会检查 __clone() 方法是否存在。如果不存在，那么它就会调用默认的 __clone() 方法，复制对象的所有属性。如果 __clone() 方法已经定义过，那么 __clone() 方法就会负责设置新对象的属性。所以在 __clone() 方法中，只需要覆盖那些需要更改的属性就可以了。

__clone() 方法不需要任何参数，下面通过一个示例来演示一下：

```

<?php
    class Website{
        public $name, $url;
        public function __construct($name, $url){
            $this -> name = $name;
            $this -> url  = $url;
        }
        public function output(){
            echo $this -> name.'，'.$this -> url.'<br>';
        }
        public function __clone(){
            $this -> name = 'PHP 教程';
            $this -> url  = 'http://c.biancheng.net/';
        }
    }

    $obj  = new Website('C 语言中文网', 'http://c.biancheng.net/php/');
    $obj2 = clone $obj;
    $obj  -> output();
    $obj2 -> output();
?>
```

运行结果如下：

C 语言中文网，http://c.biancheng.net/php/
PHP 教程，http://c.biancheng.net/

提示：如果在类中设置一个空的，访问权限为 private（私有的）的 __clone() 方法的话，可以起到禁止克隆的作用。