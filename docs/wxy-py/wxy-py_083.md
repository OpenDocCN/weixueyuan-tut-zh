# Python __init__()和 __del__()函数的用法

> 原文：[`www.weixueyuan.net/a/647.html`](http://www.weixueyuan.net/a/647.html)

__init__()（后续称之为初始化函数）是用来初始化实例对象的。每次构造一个实例对象时，都会调用该类的初始化函数。

在该初始化函数中可以初始化实例对象，或者更新类的某些属性，如该类构造出来的实例对象的个数。初始化函数也可以带参数，通过不同参数来构造不同的实例对象。

初始化函数的样式如下：

__init__(self, 参数列表)

下面定义一个没有其他参数的初始化函数：

>>> class Student:                    # 定义类 Student
...     version = "1.0"               # 类属性
...     author = "python.cn"
...     def __init__(self):           # 构造函数
...         print("__init__() is Running")
...
>>> student_a = Student()             # 构造一个对象
__init__() is Running

在 C++ 和 Java 中可以有多个初始化函数，通过不同的参数个数、不同的参数类型来选择不同的初始化函数。但在 Python 中定义多个初始化函数是没有意义的，因为最终只有一个初始化函数有效，即最后定义的初始化函数会覆盖所有前面定义的初始化函数。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):             # 第一次定义，但是被覆盖
...         print("__init__() is Running")
...     def __init__(self, age):        # 第二次定义，这才是有效的初始化函数
...         print("__init__(age) is Running")
...                                     # 类定义结束
>>> Student.__init__
<function Student.__init__ at 0x7f0431942e18>
>>> Student()      
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: __init__() missing 1 required positional argument: 'age'
>>> student_a = Student(12)
__init__(age) is Running

__del__()（后续称为销毁函数）在对象被销毁时调用，它不可以有参数。一般使用 del() 来明确销毁某个对象。销毁函数的定义方法如下：

__del__(self)

可以看到，销毁函数仅有一个表示自己的 self 参数，不能有其他参数。下面演示销毁函数在对象被销毁时被调用的情况。

>>> class Student:               # 定义类 Student
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):                     # 初始化函数
...         print("__init__() is Running")
...     def __del__(self):                      # 销毁函数
...         print("__del__() is Running")
...
        # 创建一个 Student 对象，保存到变量 student_a 中
>>> student_a = Student()     
__init__() is Running
>>> del(student_a)               # 销毁对象 student_a
__del__() is Running

如果定义的销毁函数带有除 self 之外的参数，那么在销毁对象时会导致异常。下面的例子就是在销毁函数中带了一个参数，结果导致异常。

>>> class Student:                                     # 定义类 Student
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self):                     # 初始化函数
...         print("__init__() is Running")
...     def __del__(self, arg1):        # 销毁函数，带有两个参数，这是错误的
...         print("__del__() is Running")
...         print("arg1 = " % arg1)
...
>>> student_a = Student()                      # 构造一个 Student 对象
__init__() is Running  
>>> del(student_a)                                     # 销毁该对象，抛出异常
Exception ignored in: <function Student.__del__ at 0x0000000002E740D0>
TypeError: __del__() missing 1 required positional argument: 'arg1'

下面演示类的属性的用法。该类的属性用来记录当前有多少个该类的实例对象，我们在初始化函数时将该值加 1，而在销毁函数时将该值减 1。

>>> class InstanceNum:                         # 定义类 InstanceNum
...     instance_num = 0                                # 类属性，用来记录实例对象的个数
...     def __init__(self)                      :# 初始化函数
...         InstanceNum.instance_num = InstanceNum.instance_num + 1
...         print("init(): Now Instance Number = %d" % InstanceNum.
        instance_num)
...     def __del__(self):              # 销毁函数
...         InstanceNum.instance_num = InstanceNum.instance_num - 1
...         print("del(): Now Instance Number = %d" % InstanceNum.
        instance_num)
...                                                                     # 类定义结束
>>> inst1 = InstanceNum()                      # 创建第一个实例
init(): Now Instance Number = 1
>>> inst2 = InstanceNum()                      # 创建第二个实例
init(): Now Instance Number = 2
>>> inst3 = InstanceNum()                      # 创建第三个实例
init(): Now Instance Number = 3
>>> del(inst3)                                         # 销毁第三个实例
del(): Now Instance Number = 2
>>> inst3 = InstanceNum()
init(): Now Instance Number = 3
>>> inst4 = InstanceNum()
init(): Now Instance Number = 4
>>> del(inst1)
del(): Now Instance Number = 3