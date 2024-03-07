# Python 类的定义与使用

> 原文：[`www.weixueyuan.net/a/645.html`](http://www.weixueyuan.net/a/645.html)

类最基本的功能就是定义了一个新的类型。可以使用下面的语法来定义一个空类，即一个什么属性都没有的类，一个什么事情也做不了的类。

```

class 类名:
    pass
```

这里的类名要求和变量名一样，可以由字母、数字和下划线组成，一般首字符是大写的字母，对于长度没有限制。

类定义完之后便可以使用该类来创建实例，方法如下：

实例对象名 = 类名()

下面定义一个类 Student，并且使用该类创建一个实例对象 student_a。这里使用的是 Python 2。

>>> class Student:             # 定义类 Student
...     pass
...                            # 类定义结束
>>> student_a = Student()      # 使用该类创建一个对象 student_a
>>> student_a                                  # 查看对象 student_a
<__main__.Student instance at 0x10e7c8b00>     # 该对象所属的类和 id
>>> type(student_a)            # 查看类型
<type 'instance'>
>>> dir(student_a)             # 查看该对象有什么属性
['__doc__', '__module__']

在 Python 3 中，得到的输出会有比较大的差别，如 type（对象）返回的就是该类的属性。下面是在 Python 3 中执行一样的代码得到的输出结果。

>>> class Student:             # 定义类 Student
...     pass
...                            # 类定义结束
>>> student_a = Student()      # 使用该类创建一个对象 student_a
>>> student_a                  # 查看对象 student_a
<__main__.Student object at 0x106dd3160>
>>> type(student_a)            # 查看类型，和 Python 2 不同
<class '__main__.Student'>
>>> dir(student_a)             # 查看该对象有什么属性，和 Python 2 不同
['__class__', '__delattr__', '__dict__', '__dir__',
'__doc__', '__eq__', '__format__', '__ge__',
'__getattribute__', '__gt__', '__hash__', '__init__',
'__init_subclass__', '__le__', '__lt__', '__module__',
'__ne__', '__new__', '__reduce__', '__reduce_ex__',
'__repr__', '__setattr__', '__sizeof__', '__str__',
'__subclasshook__', '__weakref__']

通过前面的例子，我们知道了两个概念，一个是类，一个是实例对象。对于预定义的类型，如整型，可以使用下面的方法来创建实例对象：

int_obj_a = int(12)

这里的类就是 int，实例对象就是 int_obj_a。我们可以创建基于一个预定义类 int 的实例对象，也可以创建一个基于自定义的类 Student 的实例对象 student_a。

这么来看就清楚了，我们以前见到的整数、字符串、列表其实都是特殊的预定义的类。那么自定义的类是否也可以使用 type() 和 id() 等函数呢？答案是可以的，并且在 Python 3 中基本上是没有区别的。

>>> type(student_a) == Student         # type()函数的用法
True
>>> isinstance(student_a, Student)     # isinstance()函数的用法
True
>>> id(student_a)                      # id()函数的用法
4410126688

但在 Python 2 中，type() 函数不能使用，所以推荐使用 isinstance()，这样代码在 Python 2 和 Python 3 中都能以预想的方式工作。下面是 Python 2 中的情形。

>>> student_a = Student()              # 创建一个对象
>>> type(student_a) == Student         # type()函数的用法，有问题
False
>>> isinstance(student_a, Student)     # isinstance()函数的用法
True
>>> id(student_a)                      # id()函数的用法
4331899648