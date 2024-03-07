# PHP new：实例化对象

> 原文：[`c.biancheng.net/view/7497.html`](http://c.biancheng.net/view/7497.html)

前面我们介绍了怎么定义一个类，但是我们要使用类中的属性和方法并不像使用变量和函数那样简单，首先要对类进行实例化才行，下面就来详细介绍一下。

## 实例化对象

将类实例化成对象非常容易，只需要使用 `new` 关键字并在后面加上一个和类名同名的方法即可。当然如果在实例化对象时不需要为对象传递参数，在 `new` 关键字后面直接用类名称即可，不需要再加上括号。

对象的实例化格式如下：

变量名 = new 类名(参数数列表);
或
变量名 = new 类名;

参数说明如下：

*   变量名：通过类所创建的一个对象的引用名称，可以通过这个名称来访问对象的成员；
*   new：关键字，表明要创建一个新的对象；
*   类名：表示新对象的类型；
*   参数列表：指定类的构造方法用于初始化对象的值，如果类中没存定义构造函数，PHP 会自动创建一个不带参数的默认构造函数。（后面我们会详细介绍）。

【示例】创建一个类并将其实例化。

```

<?php
    class Students{

    }

    $person1 = new Students();
    $person2 = new Students;
    $person3 = new Students;
    var_dump($person1);
    echo '<br>';
    var_dump($person2);
    echo '<br>';
    var_dump($person3);
?>
```

运行结果如下：

object(Students)#1 (0) { }
object(Students)#2 (0) { }
object(Students)#3 (0) { }

一个类可以实例化出多个对象，每个对象都是独立的。在上面的代码中通过 Students 类实例化出三个对象 $person1、$person2 和 $person3，相当于在内存中开辟了三份空间用于存放每个对象。

使用同一个类声明的多个对象之间是没有联系的，只能说明他们都是同一个类型，每个对象内部都有类中声明的成员属性和成员方法。就像独立的三个人，都有自己的姓名，性别和年龄的属性，每个人都有说话、吃饭和走路的方法。

## 访问对象中的成员

对象中包含成员属性和成员方法，访问对象中的成员和访问数组中的元素类似，只能通过对象的引用来访问对象中的成员。但还要使用一个特殊的运算符号`->`来完成对象成员的访问，访问对象中成员的语法格式如下所示：

变量名 = new 类名(参数);   //实例化一个类
变量名 -> 成员属性 = 值;   //为成员属性赋值
变量名 -> 成员属性;           //直接获取成员属性的值
变量名 -> 成员方法();        //访问对象中的成员方法

下面通过一个示例来演示一下：

```

<?php
    class Website{
        public $name, $url, $title;
        public function demo(){
            echo '成员方法 demo()';
        }
    }

    $student = new Website();
    $student -> name = 'C 语言中文网';
    $student -> url = 'http://c.biancheng.net/php/';
    $student -> title = '实例化对象';

    echo $student -> name.'<br>';
    echo $student -> url.'<br>';
    echo $student -> title.'<br>';
    $student -> demo();
?>
```

运行结果如下：

C 语言中文网
http://c.biancheng.net/php/
实例化对象
成员方法 demo()