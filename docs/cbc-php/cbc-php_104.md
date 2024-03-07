# PHP 魔术方法（附带实例）

> 原文：[`c.biancheng.net/view/6230.html`](http://c.biancheng.net/view/6230.html)

在面向对象编程中，PHP 提供了一系列的魔术方法，这些魔术方法为编程提供了很多便利，在 PHP 中的作用是非常重要的。PHP 中的魔术方法通常以 __（两个下划线）开始，并且不需要显式的调用而是在某种特定条件下自动调用的。

前面我们介绍的构造函数 __construct 和析构函数 __destruct 就属于 PHP 中的魔术方法。PHP 中的魔术方法如下表所示：

| 魔术方法 | 作用 |
| __construct() | 实例化类时自动调用 |
| __destruct() | 类对象使用结束时自动调用 |
| __set() | 在给未定义的属性赋值时自动调用 |
| __get() | 调用未定义的属性时自动调用 |
| __isset() | 使用 isset() 或 empty() 函数时自动调用 |
| __unset() | 使用 unset() 时自动调用 |
| __sleep() | 使用 serialize 序列化时自动调用 |
| __wakeup() | 使用 unserialize 反序列化时自动调用 |
| __call() | 调用一个不存在的方法时自动调用 |
| __callStatic() | 调用一个不存在的静态方法时自动调用 |
| __toString() | 把对象转换成字符串时自动调用 |
| __invoke() | 当尝试把对象当方法调用时自动调用 |
| __set_state() | 当使用 var_export() 函数时自动调用，接受一个数组参数 |
| __clone() | 当使用 clone 复制一个对象时自动调用 |
| __debugInfo() | 使用 var_dump() 打印对象信息时自动调用 |

前面我们已经介绍过了《构造函数》和《析构函数》，下面我们再来介绍几个常用的魔术方法。

## 1、__set() 方法

在为当前环境下未定义或不可见的类属性赋值时，会自动调用 __set() 方法。定义该方法的语法格式如下：

public function __set($key, $value){
    ... ... ;
}

其中，参数 $key 是要操作的变量名称，$value 为变量 $key 的值。

【示例】下面使用简单的示例来演示一下 __set() 方法的使用。

```

<?php
    class Website{
        public $name;
        private $url;
        public function __set($key, $value){
            echo '为“'.$key.'”赋值“'.$value.'”失败！<br>';
        }
    }

    $object          = new Website();
    $object -> name  = 'C 语言中文网';
    $object -> url   = 'http://c.biancheng.net/php/';
    $object -> title = 'PHP 教程';
?>
```

运行结果如下：

为“url”赋值“http://c.biancheng.net/php/”失败！
为“title”赋值“PHP 教程”失败！

利用 __set() 方法的特性，我们可以通过 __set() 方法为类中的使用 private 关键词修饰的属性进行赋值或修改。如下所示：

```

<?php
    class Website{
        public $name;
        private $url = '';
        public function __set($key, $value){
            if(isset($this->$key)){
                $this -> $key = $value;
            }else{
                echo '为“'.$key.'”赋值“'.$value.'”失败！<br>';
            }
        }
        public function getUrl(){
            echo $this -> url;
        }
    }

    $object          = new Website();
    $object -> name  = 'C 语言中文网';
    $object -> url   = 'http://c.biancheng.net/php/';
    $object -> title = 'PHP 教程';
    $object -> getUrl();
?>
```

运行结果如下：

为“title”赋值“PHP 教程”失败！
http://c.biancheng.net/php/

## 2、__get() 方法

在调用或获取当前环境下未定义或不可见的类属性时，会自动调用 __get() 方法，定义该方法的语法格式如下：

public function __get($name){
    ... ... ;
}

参数 $name 为要操作的变量名称。

【示例】下面使用简单的示例来演示一下 __get() 方法的使用。

```

<?php
    class Website{
        public $url = 'http://c.biancheng.net/php/';
        private $name = 'C 语言中文网';
        public function __get($name){
            echo '获取：“'.$name.'”失败！';
        }
    }

    $object = new Website();
    echo $object -> url.'<br>';
    echo $object -> name.'<br>';
    echo $object -> title.'<br>';
?>
```

运行结果如下：

http://c.biancheng.net/php/
获取：“name”失败！
获取：“title”失败！

## 3、__isset() 方法

当在类外部对类中不可访问或不存在的属性使用 isset() 或 empty() 函数时，会自动调用 __isset() 方法，该方法的语法格式如下：

public function __isset($name){
    ... ... ;
}

参数 $name 为要访问的属性名称。

isset() 函数可以检查一个变量是否存在并且不为 NULL，传入一个变量作为参数，如果传入的变量存在则传回 true，否则传回 false。

empty() 函数可以检查一个变量是否为空，同样需要传入一个变量作为参数，如果变量并不存在，或者变量的值等于 FALSE，那么这个变量会被认为不存在。

通过前面的学习我们知道，类中的公有成员可以在类外访问，而私有成员则无法在类外访问。也就是说，我们可以使用 isset() 或 empty() 函数来检查类中的公有属性是否存在，而对类中的私有属性这两个函数就无效了。

如果想要使用 isset() 或 empty() 函数对类中的私有属性进行检测的话，我们只需要在类中添加一个 __isset() 方法就可以了，当在类外部使用 isset() 或 empty() 函数时，会自动调用类里面的 __isset() 方法。

【示例】下面使用简单的示例来演示一下 __isset() 方法的使用。

```

<?php
    class Website{
        public $url = 'http://c.biancheng.net/php/';
        private $name = 'C 语言中文网';
        public function __isset($name){
            if(property_exists('Website', $name)){
                echo '成员属性：“'.$name.'”存在！<br>';
            }else{
                echo '成员属性：“'.$name.'”不存在！<br>';
            }
        }
    }

    $object = new Website();
    isset($object -> title);
    isset($object -> name);
?>
```

运行结果如下：

成员属性：“title”不存在！
成员属性：“name”存在！

提示：property_exists() 函数可以用来检测类中是否定义了该属性，语法格式为 property_exists($class_name，$property_name)，其中 $class_name 为字符串形式的类名，即判断类 class_name 中是否定义了 property_name 属性。

## 4、__unset() 方法

当在类外部对类中不可访问或不存在的属性使用 unset() 函数时，__unset() 方法会被自动调用，该方法的语法格式如下：

public function __unset($name){
    ... ... ;
}

参数 $name 为要访问的属性名称。

我们先来看一下 unset() 函数，unset() 函数的作用是删除指定的变量，需要传入一个或多个变量作为参数，另外，该函数没有返回值。

同样，我们也可以使用 unset() 函数在类外部去删除类中的成员属性。与上面介绍的 __isset() 方法相似，如果要删除类中的公有属性的话直接使用 unset() 函数即可；如果要删除类中的私有属性的话，则需要在类中添加一个 __unset() 方法。

【示例】下面使用简单的示例来演示一下 __unset() 方法的使用。

```

<?php
    class Website{
        public $url = 'http://c.biancheng.net/php/';
        private $name = 'C 语言中文网';
        public function __unset($name){
            if(property_exists('Website', $name)){
                unset($this->$name);
                echo '移除成员属性：“'.$name.'”成功！<br>';
            }else{
                echo '成员属性：“'.$name.'”不存在！<br>';
            }
        }
    }

    $object = new Website();
    unset($object->url);
    unset($object->name);
    unset($object->abc);
?>
```

运行结果如下：

移除成员属性：“name”成功！
成员属性：“abc”不存在！

## 5、__call() 方法

当调用类中一个不可访问或不存在的方法时，__call() 方法会被调用。该方法的语法格式如下：

public function __call($name, $arguments){
    ... ... ;
}

其中，$name 为要调用的方法名称，$arguments 为传递给 $name 的参数所组成的数组。

当调用的方法不存在时会自动调用 __call() 方法，程序会继续执行下去，从而可以避免当调用方法不存在时产生错误所导致的程序终止。

【示例】下面使用简单的示例来演示一下 __call() 方法的使用。

```

<?php
    class Website{
        public function say(){
            echo 'Welcome C 语言中文网！<br>';
        }
        public function __call($name, $arguments){
            echo '你所调用的方法：'.$name;
            if(!empty($arguments)){
                echo '【以及参数：';
                print_r($arguments);
                echo '】';
            }
            echo ' 不存在！<br>';
        }
    }

    $obj = new Website();
    $obj -> say();
    $obj -> url('http://c.biancheng.net/php/');
    $obj -> title();
?>
```

运行结果如下：

Welcome C 语言中文网！
你所调用的方法：url【以及参数：Array ( [0] => http://c.biancheng.net/php/ ) 】 不存在！
你所调用的方法：title 不存在！

## 6、__clone() 方法

可以使用 clone 关键字复制对象，当复制完成时，如果定义了 __clone() 方法，则新创建的对象（复制生成的对象）中的 __clone() 方法会被自动调用，通过该方法我们可以做一些必要的操作。__clone() 方法的语法格式如下：

public function __clone(){
    ... ... ;
}

该函数不需要传入参数。

【示例】下面使用简单的示例来演示一下 __clone() 方法的使用。

```

<?php
    class Website{
        public function say(){
            echo 'Welcome C 语言中文网！<br>';
        }
        public function __clone(){
            echo '对象克隆成功<br>';
        }
    }

    $obj = new Website();
    $obj2 = clone $obj;
    echo '<pre>';
    var_dump($obj, $obj2);
?>
```

运行结果如下：

对象克隆成功
object(Website)#1 (0) {
}
object(Website)#2 (0) {
}