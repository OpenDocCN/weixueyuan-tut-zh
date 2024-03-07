# Python 类的派生（派生类）

> 原文：[`www.weixueyuan.net/a/659.html`](http://www.weixueyuan.net/a/659.html)

派生是面向对象编程的基本概念，它赋予了我们基于某个基础类来轻松创建新类的能力。使用派生类的方法，我们可以复用他人的代码，不必从 0 开始创建自己的轮子。

定义一个基于某个现有类的新类的方法如下：

```

def 新类(现有的类):
    pass
```

现有的类，有时也称为基类、父类；新类也称为派生类、子类。派生类继承了基类的所有属性。

下面的例子中，车辆是基类，汽车和自行车是派生类，它们都继承了车辆的全部成员。

>>> class Vehicle:                    # 车辆类
...     def __init__(self):
...         self.cycle_number = 0
...         self.price = 0
...         self.producer = ""
...     def get_price(self):
...         return self.price
...     def set_price(self, price):
...         self.price = price
...
>>> class Bicycle(Vehicle):           # 自行车类，派生于车辆类
...     def __init__(self):
...         pass
...
>>> class Car(Vehicle):               # 汽车类，派生于车辆类
...     def __init__(self):
...         pass
...
>>> car_a = Car()
>>> bicycle_a = Bicycle()
>>> car_a.set_price(100000)           # set_price()就是从基类继承而来的
>>> car_a.get_price()                 # get_price()也是从基类继承而来的
100000
>>> bicycle_a.set_price(200)
>>> bicycle_a.get_price()
200

需要注意的是，在派生类的构造函数中要明确调用其父类的构造函数。如果不显示调用，基类的构造函数不会被执行。这点和 C++、Java 不同。在 C++ 和 Java 中，基类的构造函数在创建派生类时是自动调用的。

>>> class Vehicle:                 # 车辆类
...     def __init__(self):
...         print("Constructor of Vehicle is Running")
...
>>> class Bicycle(Vehicle):
...     def __init__(self):
...         pass
...
>>> bicycle_a = Bicycle ()

从上面可以看到，基类 Vehicle 的构造函数并没有运行。显示调用父类的构造函数可以使用 super().__init__()。

>>> class Vehicle:             # 车辆类
...     def __init__(self):
...         print("Constructor of Vehicle is Running")
...
>>> class Bicycle(Vehicle):
...     def __init__(self):
...         super().__init__()
...
>>> bicycle_a = Bicycle()
Constructor of Vehicle is Running

## 多重继承

在 Python 中，一个类可以派生自多个现有类，该特性就是多重继承。Python 的这个特性和 C++ 类似，有些语言如 Java 是不支持多重继承的。

多重继承的语法格式如下：

```

class 新类(现有类 1, 现有类 2, ....):
    pass
```

下面定义 3 个类:

1.  一个类是 Birds，表示所有的鸟类，鸟类都会下蛋，所以为其提供一个 lay_egg() 接口函数；
2.  第二个类是 CanSwim，表示会游泳的水生类，可以表示鱼，也可以表示海豹等，为其提供一个 swim() 接口函数，表示在游泳；
3.  第三个是企鹅类 Penguin，企鹅会下蛋，也会游泳，可以让它继承自 Birds 和 CanSwim。

这样便会发现 Penguin 类自动有了 swim() 和 lay_egg() 这两个接口函数，而且具体实现就是分别在 CanSwim 和 Birds 中定义的。

下面是完整的代码。

>>> class Birds:
...     """鸟类"""
...     def lay_egg(self):
...         print(u"下蛋中")
...
>>> class CanSwim:
...     """水生类"""
...     def swim(self):
...         print(u"游泳中")
...
>>> class Penguin(CanSwim, Birds):
...     """ 企鹅 """
...     def __init__(self):
...         pass
...
>>> pg_obj1 = Penguin()
>>> pg_obj1.swim()
游泳中
>>> pg_obj1.lay_egg()
下蛋中

在多重继承中，如果某个函数在其两个或者多个基类中都定义了，那么应该使用哪个呢？还以上面的例子为例，不管哪类生物，都有诞生和死亡，这里鸟类和水生类都加上 die() 接口函数，下面来看看究竟哪个 die() 会被执行。

>>> class Birds:
...     """鸟类"""
...     def lay_egg(self):
...         print(u"下蛋中")
...     def die(self):
...         print(u"鸟类死亡")
...
>>> class CanSwim:
...     """水生类"""
...     def swim(self):
...         print(u"游泳中")
...     def die(self):
...         print(u"水生类死亡")
...
>>> class Penguin(CanSwim, Birds):   # 定义了企鹅类
...     """ 企鹅 """                  # 其两个父类都定义了 die()接口函数
...     def __init__(self):
...         pass
...
>>> rb_obj1 = Penguin()
>>> rb_obj1.swim()
游泳中
>>> rb_obj1.lay_egg()
下蛋中
>>> rb_obj1.die()                 # 最后使用了 CanSwim 的 die()接口函数
水生类死亡

可以发现第一个基类的接口函数被继承下来，即 CanSwim 类的 die() 被继承下来。如果将两个基类的顺序调换一下，就会发现继承的 die() 接口函数将来自 Birds 类。

>>> class Birds:
...     """鸟类"""
...     def lay_egg(self):
...         print(u"下蛋中")
...     def die(self):
...         print(u"鸟类死亡")
...
>>> class CanSwim:
...     """水生类"""
...     def swim(self):
...         print(u"游泳中")
...     def die(self):
...         print(u"水生类死亡")
...                                                                             # 类定义结束
>>> class Penguin(Birds, CanSwim):   # 调换了基类的顺序
...     """ 企鹅 """
...     def __init__(self):
...         pass
...
>>> rb_obj1 = Penguin()
>>> rb_obj1.swim()
游泳中
>>> rb_obj1.lay_egg()
下蛋中
>>> rb_obj1.die()                 # 最后使用了 Birds 的 die()接口
鸟类死亡                                # 这个和修改前的完全不同

对于属性也是一样的，如果多个基类同时定义了相同的属性，那么第一个基类的属性有效。假定给 Bird 添加一个属性 food，其值为 worm；同时给 CanSwim 也添加一个同样的属性 food，其值为 fish。现在 Penguin 同时继承自 Birds 和 CanSwim，那么它继承的 food 值就是第一个基类的值，也就是 worm。下面是该种情况的具体演示：

>>> class Birds:
...     """鸟类"""
...     food = "worm"
...
>>> class CanSwim:
...     """水生类"""
...     food = "fish"
...
>>> class Penguin(Birds, CanSwim):
...     """ 企鹅 """
...     def __init__(self):
...         pass
...
>>> rb_obj1 = Penguin()
>>> rb_obj1.food
'worm'