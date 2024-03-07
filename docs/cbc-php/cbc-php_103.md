# PHP 设计模式之单例模式与工厂模式

> 原文：[`c.biancheng.net/view/7563.html`](http://c.biancheng.net/view/7563.html)

设计模式不是一套具体的语言框架，而是一种行之有效的编码规范，是前人经过反复使用并总结出来的编写代码的经验。使用设计模式的目的是为了提高代码可重用性，让代码更容易被他人理解，同时保证代码可靠性。合理使用设计模式有助于我们更加深入地理解面向对象思维。

其实，大家在编码的过程中肯定也用到了一些与这些设计模式类似的编码方法，只是不知道这些方法可以被抽象到“模式”这么一个高度。

本节我们就来简单地了解一下在 PHP 编码过程中经常会用到的一些设计模式。

## 1、工厂模式

工厂模式是一种专门用来创建其它对象的类（称为“工厂类”），根据传递参数的不同，来创建不同类的对象。我们可以使用工厂类创建对象，而不是直接使用 new。

工厂类中至少有一个公共的静态方法（称为“工厂方法”），静态方法接受一个参数，根据这个参数来创建不同类的对象。

【示例】定义加、减、乘、除四个类，并通过工厂类调用它们。

```

<?php
    // 加法类
    class Add {
        public function getValue($num1, $num2){
            return $num1 + $num2;
        }
    }

    // 减法类
    class Sub {
        public function getValue($num1, $num2){
            return $num1 - $num2;
        }
    }

    // 乘法类
    class Mul {
        public function getValue($num1, $num2){
            return $num1 * $num2;
        }
    }

    // 除法类
    class Div {
        public function getValue($num1, $num2){
            try {
                if ($num2==0){
                    throw new Exception("除数不能为 0");
                }else {
                    return $num1/$num2;
                }
            }catch (Exception $e){
                echo "错误信息：".$e->getMessage();
            }
        }
    }

    // 工程类，主要用来创建对象
    // 功能：根据输入的运算符号，工厂就能实例化出合适的对象
    class Factory {
        public static function createObj($operate){
            switch ($operate){
                case '+':
                    return new Add();
                    break;
                case '-':
                    return new Sub();
                    break;
                case '*':
                    return new Mul();
                    break;
                case '/':
                    return new Div();
                    break;
            }
        }
    }
    $test   = Factory::createObj('/');
    $result = $test->getValue(23,3);
    echo $result;
?>
```

运行结果如下：

7.6666666666667

## 2、单例模式

单例模式也叫单子模式，是一种常用的软件设计模式。 在应用这个模式时，可以确保一个类只能创建一个对象，这么做可以极大节省内存空间，有利于我们协调系统的整体行为。

使用单例模式创建的类（“单例类”）不能再其它类中直接实例化，只能被其自身实例化。它不会创建实例副本，而是会向单例类内部存储的实例返回一个引用。

单例模式一个主要应用场合就是应用程序与数据库打交道的场景，在一个应用中会存在大量的数据库操作，针对数据库句柄连接数据库的行为，使用单例模式可以避免大量的 new 操作，因为每一次 new 操作都会消耗系统和内存的资源。

实现单例模式的思路（三私一公）：

*   私有的静态的对象实例；
*   私有的构造方法，在类外不能使用 new 创建对象；
*   私有的克隆方法，在类外不能使用 clone 克隆对象；
*   公共的静态的创建对象实例的方法。

【示例】使用单例模式创建一个数据库连接类。

```

<?php
    class Mysql{
        //该属性用来保存实例
        private static $conn;
        // 私有的构造函数，防止在类外创建对象
        private function __construct(){
            self::$conn = mysqli_connect('localhost','root','root');
        }
        // 公共的创建对象的方法，如果不存在一个这个类的实例属性，就创建一个，否则就取这个实例属性。
        public static function getInstance(){
            if(!(self::$conn instanceof self)){
                self::$conn = new self;
            }
            return self::$conn;
        }
        // 私有的克隆方法，防止在类外 clone 对象
        public function __clone(){}
        //防止反序列化后创建对象
        private function __wakeup(){
            trigger_error('Unserialized is not allowed !');
        }
    }
    //只能这样取得实例，不能 new 和 clone
    $obj1 = Mysql::getInstance();
    $obj2 = Mysql::getInstance();
    $obj3 = Mysql::getInstance();
    var_dump($obj1, $obj2, $obj3);
?>
```

运行结果如下：

object(Mysql)#1 (0) {
}
object(Mysql)#1 (0) {
}
object(Mysql)#1 (0) {
}

通过运行结果可以看出，我们多次调用 getInstance() 方法并没有创建多个对象，所以说使用单例模式可以保证一个类只能创建一个对象，不能创建第二个对象。