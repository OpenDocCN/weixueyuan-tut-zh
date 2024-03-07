# PHP extends：继承

> 原文：[`c.biancheng.net/view/7510.html`](http://c.biancheng.net/view/7510.html)

面向对象编程（OOP）的一大好处就是，可以使用一个类继承另一个已有的类，被继承的类称为父类或基类，而继承这个父类的类称为子类。子类可以继承父类的方法和属性，因此通过继承可以提高代码的重用性，也可以提高软件的开发效率。

子类可以增加父类之外的新功能，因此也可以将子类称为父类的“扩展”。此外，子类还可以继承父类的构造函数，当子类被实例化时，PHP 会先在子类中查找构造函数。如果子类有自己的构造函数，PHP 会先调用子类中的构造函数。当子类中没有时，PHP 则会去调用父类中的构造函数。

在 PHP 中，类的继承需要通过 extends 关键字来实现。语法格式如下所示：

class 子类名 extends 父类名{
    ... ...
}

在 C++ 中，一个子类可以继承一个基类，也可以继承多个基类。继承一个基类称为单继承；继承多个基类称为多继承。但在 PHP 中没有多继承，只能使用单继承模式。也就是说，一个类只能直接从另一个类中继承数据。但一个类可以有多个子类。

通过前面的学习我们知道，类中的成员属性和方法都需要使用访问权限修饰符来修饰，这是 PHP 面向对象编程中的一个重要特性。不同访问修饰符的作用及其之间的区别如下表所示：

|   | public（默认） | private | protected |
| 同一个类中访问 | √ | √ | √ |
| 在子类中访问 | √ |   | √ |
| 在类的外部访问 | √ |   |   |

## 1、继承 public 成员

类的成员都有不同的访问权限，由访问控制关键字来控制的。在 PHP 中，父类所有使用 public 修饰的成员均可以被子类继承。

【示例】定义一个基类，使用另一类来继承这个基类，同时在尝试使用子类调用基类中的成员方法。

```

<?php
    class Website{
        public $name, $url, $title;
        public function __construct(){
            echo '------基类中的构造函数------<br>';
        }
        public function demo(){
            echo '基类中的成员方法<br>';
        }
    }

    class ClassOne extends Website{

    }

    class ClassTwo extends Website{
        public function __construct(){
            echo '------子类中的构造函数------<br>';
        }
    }

    $object = new ClassOne();
    $object -> demo();
    $object2 = new ClassTwo();
    $object2 -> demo();
?>
```

运行结果如下：

------基类中的构造函数------
基类中的成员方法
------子类中的构造函数------
基类中的成员方法

## 2、继承 protected 成员

很多情况下有些类继承的属性是不想在类外部被访问的，这时就可以把这个成员声明为一个保护成员，也就使用 protected 修饰这个成员。受保护的成员不可以在类外部访问到，但是可以在子类的内部访问，也是就说我们可以在子类设置一个成员函数来访问这个受保护成员。

【示例】将上面示例的代码略作调整，演示类中 protected 成员的继承情况。

```

<?php
    class Website{
        public $name, $url, $title;
        public function __construct(){
            echo '------基类中的构造函数------<br>';
        }
        protected function demo(){
            echo '基类中的成员方法<br>';
        }
    }

    class ClassOne extends Website{

    }

    class ClassTwo extends Website{
        public function __construct(){
            echo '------子类中的构造函数------<br>';
        }
        public function test(){
            $this -> demo();
        }
    }

    $object = new ClassOne();
    // $object -> demo();      // 在子类中调用父类使用 protected 修饰的成员方法会报错
    $object2 = new ClassTwo();
    $object2 -> test();
?>
```

运行结果如下：

------基类中的构造函数------
------子类中的构造函数------
基类中的成员方法

在子类中调用父类使用 protected 修饰的成员方法会报错，如果去掉上面代码中第 26 行的注释，程序会报错并停止运行，如下所示：

Fatal error: Uncaught Error: Call to protected method Website::demo() from context '' ··· ···

## 3、private 成员

类中使用 private 修饰的成员被称为私有成员。父类中的私有成员不会被子类继承，因此不能被子类访问到，下面来看一个示例。

```

<?php
    class Website{
        private function demo(){
            echo '基类中的成员方法<br>';
        }
    }

    class ClassOne extends Website{
        public function test(){
            $this -> demo();
        }
    }

    $object = new ClassOne();
    $object -> test();
?>
```

运行结果如下：

Fatal error: Uncaught Error: Call to private method Website::demo() from context 'ClassOne' ··· ···

在子类中调用父类中使用 private 关键字修饰的成员时，程序会报错并终止运行。