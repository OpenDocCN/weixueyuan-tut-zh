# Python 类的私有属性

> 原文：[`www.weixueyuan.net/a/656.html`](http://www.weixueyuan.net/a/656.html)

在 C++ 和 Java 中，属性（成员）有访问控制，如 public 表示任意代码可以访问该属性，private 表示仅当前类的代码才可以访问该属性。

Python 中没有这个访问控制符，但是可以在属性前面加上 __ 两个下划线来表示该属性不能在类外部访问。一般的属性都是可以在类的外部访问的，但是以两个下划线（__）开头的属性是不能在类的外部访问的。

下面的例子演示了这个情况：

>>> class Student:                     # 定义一个类
...   __version = "1.0"                # 私有的属性
...   version = "1.1"                  # 普通的属性
...                                    # 类属性定义完毕
...   def __init__(self):              # 初始化函数
...     self.__private_attr = 88       # 定义了一个私有的属性 __private_attr
...     self.private_attr = 89         # 定义了一个普通的属性 private_attr
...                                    # 类定义结束
>>> Student.version                    # 查看类属性
'1.1'
>>> Student.__version                  # 私有的类属性是不能访问的
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: class Student has no attribute '__version'
>>> student_obj1 = Student()           # 创建一个类实例对象
>>> student_obj1.version               # 查看普通属性
'1.1'
>>> student_obj1.__version             # 查看私有属性，抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: Student instance has no attribute '__version'
>>> student_obj1.private_attr          # 查看普通属性
89
>>> student_obj1.__private_attr        # 查看私有属性，抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: Student instance has no attribute '__private_attr'