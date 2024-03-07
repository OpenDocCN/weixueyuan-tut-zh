# Python getattr()、setattr()、hasattr()和 delattr()的用法

> 原文：[`www.weixueyuan.net/a/652.html`](http://www.weixueyuan.net/a/652.html)

除了使用前面介绍的方法来操作属性外，还有一套通用的方法来操作属性。这种方法类似于静态反射机制，即在不知道对象具体定义的情况下，也能通过查询等方式得到该对象的一些情况。

如我们得到一个对象 a，但对象 a 是什么类型、包含哪些属性我们都不知道，那么就可以用 dir() 来查看其所拥有的属性，这个 dir() 使用的就是本节要介绍的通用属性接口函数。

## 1、getattr(对象, 属性名)：读属性

这个等效于“对象.属性”。

>>> class Student:                   # 定义类 Student
...     version = "1.0"              # 类属性 version
...     author = "python.cn"         # 类属性 author
...     def __init__(self):
...         self.score = 0
...                                                                             # 类定义结束
>>> getattr(Student, 'version')       # 得到 Student 类的属性 version
'1.0'
>>> student_a = Student()             # 定义一个对象
>>> getattr(student_a, 'version')     # 得到该对象的 version 属性
'1.0'
>>> getattr(student_a, 'score')       # 得到该对象的 score 属性
0

## 2、setattr(对象, 属性名, 属性值)：修改属性

这个等效于“对象。属性=属性值”。下面的例子演示了如何修改属性。

>>> class Student:                                             # 定义类 Student
...     version = "1.0"                                 # 类属性 version
...     author = "python.cn"                    # 类属性 author
...     def __init__(self):
...         self.score = 0
...                                                                             # 类定义结束
>>> setattr(student_a, 'score', 99)    # 修改属性 score 的值
>>> getattr(student_a, 'score')                # 查看修改后的值
99
>>> setattr(student_a, 'new_attr', 'new_attr_val')             # 添加属性
>>> getattr(student_a, 'new_attr')             # 查看新添加的属性值
'new_attr_val

## 3、hasattr(对象, 属性名)：是否包含某个属性

判断某个对象是否包含某个属性。由于 Python 是动态语言，某个对象所包含的属性也是随时间而变化的，这时就可以用该函数来判断某个对象是否包含有特定的属性。

>>> class Student:                     # 定义类 Student
...   version = "1.0"
...                                                                             # 类定义结束
>>> hasattr(Student, 'version')        # 类是否包含属性 version
True
>>> student_obj1 = Student()           # 创建一个类实例对象
>>> hasattr(student_obj1, 'version')   # 类实例对象是否包含 version 属性
True

## 4、delattr(对象, 属性名)：删除某个属性

其作用和 del(对象.属性) 是一样的。

>>> class Student:                    # 定义类
...   version = "1.0"
...                                                                             # 类定义结束
>>> hasattr(Student, 'version')        # 是否包含属性 version
True
>>> student_obj1 = Student()           # 创建类实例对象
>>> hasattr(student_obj1, 'version')   # 实例对象是否包含属性 version
True
>>> hasattr(student_obj1, 'new_attr')  # 实例对象是否包含属性 new_attr
False
>>> setattr(student_obj1, 'new_attr', 88)      # 添加属性 new_attr
>>> hasattr(student_obj1, 'new_attr')  # 实例对象是否包含属性 new_attr
True                                   # 包含该属性
>>> delattr(student_obj1, 'new_attr')  # 删除属性 new_attr
>>> hasattr(student_obj1, 'new_attr')  # 实例对象是否包含属性 new_attr
False                                  # 没有该属性了