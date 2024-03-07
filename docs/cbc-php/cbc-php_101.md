# PHP $this：当前对象

> 原文：[`c.biancheng.net/view/7515.html`](http://c.biancheng.net/view/7515.html)

在 PHP 面向对象编程中，对象一旦被创建，在对象中的每个成员方法里面都会存在一个特殊的对象引用“$this”。成员方法属于哪个对象，“$this”就代表哪个对象，与连接符`->`联合使用，专门用来完成对象内部成员之间的访问。如下所示：

$this -> 成员属性;
$this -> 成员方法(参数列表);

比如在 Website 类中有一个 $name 属性，我们可以在类中使用如下方法来访问 $name 这个成员属性：

$this -> name;

需要注意的是，在使用 $this 访问某个成员属性时，后面只需要跟属性的名称即可，不需要`$`符号。另外，$this 只能在对象中使用，其它地方不能使用 $this，而且不属于对象的东西 $this 也调用不了，可以说没有对象就没有 $this。

【示例】使用 $this 调用类中的属性和方法。

```

<?php
    class Website{
        public $name;
        public function __construct($name){
            $this -> name = $name;
            $this -> name();
        }
        public function name(){
            echo $this -> name.'<br>';
            $this -> url();
        }
        public function url(){
            echo 'http://c.biancheng.net/php/<br>';
            $this -> title();
        }
        public function title(){
            echo 'PHP 入门教程<br>';
        }
    }

    $object = new Website('C 语言中文网');
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
PHP 入门教程