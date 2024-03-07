# Python self 的用法

> 原文：[`www.weixueyuan.net/a/651.html`](http://www.weixueyuan.net/a/651.html)

对象属性在类外面使用“对象名。属性”来表示，在类里面使用“self.属性”来表示。对于对象的属性也存在增删改查操作，如可以用赋值语句来修改或者添加属性，通过 del() 来删除对象属性。

添加属性对于 Python 这种动态语言来说非常简单，它对添加的时间没有要求，可以在运行时添加，也可以在创建时添加。而对于 C++ 和 Java 这类静态语言来说，属性的个数和种类在类定义时就已经确定，而且以后不能修改，在运行时仅可以修改属性的值。

下面是在构造时添加属性的例子。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):
...         self.name = ""
...                              # 类定义结束
>>> student_a = Student()        # 创建一个类实例对象
>>> student_a.name               # 查看 name 的值
''

下面是在成员函数中添加属性的例子。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):
...         self.name = ""        # 新添加的属性 name
...     def add_attr_age(self, val):
...         self.age = val
...
>>> student_a = Student()
>>> hasattr(student_a, "age")
False
>>> student_a.add_attr_age(18)
>>> hasattr(student_a, "age")
True

当然也可以在类的外面为对象添加属性。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):
...         self.name = ""
...                                  # 类定义结束
>>> student_a = Student()
>>> hasattr(student_a, "age")        # 现在该实例对象还没有属性 age
False
>>> student_a.age = 18               # 给该实例对象添加新的属性 age
>>> hasattr(student_a, "age")
True

这就是作为动态语言的优势，对象的属性不是事先定义好的，其属性可以在运行过程中添加，也可以在运行过程中修改和删除。

其实在 Python 中，变量和函数的地位是相同的，这也意味着我们可以添加对象的方法。但需要注意的是，对象方法的第一个参数一定是 self，表示当前对象。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):
...         self.name = ""
...     def method1(self):
...         print("Inside method1()")
...     def method2(self):
...         print("Inside method2()")
...     def method(self):
...         print("Old One")
...     def update_method(self, val):
...         if val == 1:
...             self.method = self.method1      # 修改 method 方法
...         else:
...             self.method = self.method2
...
>>> student_a = Student()
>>> student_a.method()                 # 修改之前
Old One
>>> student_a.update_method(1)         # 修改为 method1 这个函数
>>> student_a.method()
Inside method1()
>>> student_a.update_method(2)         # 修改为 method2 这个函数
>>> student_a.method()
Inside method2()

当然也可以将类外部的函数变成类的函数，这时需要指定外部函数的 __self__ 属性，该属性表明该函数所属的对象。下面的代码简单演示了该用法，不过最好的方法还是使用闭包来实现。

>>> def obj_method():
...     print("Inside obj_method()")
...     print("obj.name = [%s]" % obj_method.__self__.name)
...                                                                             # 函数定义结束
>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):
...         self.name = ""
...     def update_method(self, val):
...         self.new_method = val
...         val.__self__ = self
...                                                                             # 类定义结束
>>> student_a.get_highest_score                # 这是一个普通函数
<function get_highest_score at 0x1047cc848>
>>> student_a.set_score                                        # 这是一个对象的方法
<bound method Student.set_score of <__main__.Student instance at
        0x1047c7e18>>
>>> student_a = Student()
>>> hasattr(student_a, "new_method")
False
>>> student_a.update_method(obj_method)
>>> hasattr(student_a, "new_method")
True
>>> student_a.new_method()
Inside obj_method()
obj.name = []