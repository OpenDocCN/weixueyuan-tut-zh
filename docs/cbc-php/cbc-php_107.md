# PHP final：最终类和最终方法

> 原文：[`c.biancheng.net/view/6234.html`](http://c.biancheng.net/view/6234.html)

在 PHP5 中新增加了 final 关键字，它可以在类或类中成员方法前使用，但是不能用来修饰成员属性。final 的中文含义是最终的、最后的，所以使用 final 修饰的类可以称为“最终类”，使用 final 修饰的成员方法可以称为“最终方法”。其语法格式如下：

final class 类名{                   // 最终类
    ... ... ;
}
final function 方法名(){       // 最终方法
    ... ... ;
}

final 关键字的作用如下：

*   使用 final 修饰的类，不能被继承；
*   类中使用 final 修饰的成员方法，在子类中不能覆盖（重写）该方法。

如果某个类不想被继承，那么就可以使用 final 来修饰这个类。使用 final 修饰的类不能有子类，也就不能对它进行拓展。如果想要继承一个使用 final 修饰的类，程序将会报错，如下所示：

```

<?php
    final class DemoClass{
    }

    class DemoClass2 extends DemoClass{
    }
?>
```

报错信息如下所示：

Fatal error: Class DemoClass2 may not inherit from final class (DemoClass) in ... ...

如果类中的某个方法已经很完善了，不需要再被重写，那么就可以使用 final 来修饰这个方法。如果在子类中试图重写这个使用 final 修饰的方法，程序同样会出现错误，如下所示：

```

<?php
    class DemoClass{
        final public function say(){
            echo 'C 语言中文网';
        }
    }

    class DemoClass2 extends DemoClass{
        public function say(){
            echo 'http://c.biancheng.net/php/';
        }
    }
?>
```

报错信息如下所示：

Fatal error: Cannot override final method DemoClass::say() in ... ...

提示：final 关键字应该放在其它修饰词（例如 public、static 等）之前使用。