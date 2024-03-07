# Python 只读属性的设置（2 种方法）

> 原文：[`www.weixueyuan.net/a/657.html`](http://www.weixueyuan.net/a/657.html)

默认情况下，实例对象的所有成员都是可以读和写的。但有时希望做一些限制，如只能读不能修改，或者检查修改的值必须符合类型要求或者范围要求。

实现该功能的方法有多种，这里主要介绍使用 @property 修饰符和定义 __setattr__() 函数两种方法。

## 使用@property 修饰符

该方法是通过 @property 修饰符来定义一个特殊属性，对该属性进行读操作就是调用对应函数并得到该函数的返回值。
需要注意的是，不能对该属性进行写操作。

>>> class Student:                       # 定义类
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self, name, gender, age):
...         self.name = name
...         self.gender = gender
...         self._age = age
...     @property                         # age 是一个属性，在类的外面是只读
...     def age(self):
...         return self._age
...                                       # 类定义结束
>>> student_a = Student('alex', 0, 18)
>>> student_a.age                         # 得到属性的值
18
>>> student_a.age = 12                    # 不能修改
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
AttributeError: can't set attribute

## 重新定义 __setattr__()函数

另外一种方法是通过定义 __setattr__() 函数来检查。该函数在成员属性被赋值时被调用。它有 3 个参数，第一个是 self，第二个是成员属性名，第三个是新的值。其基本定义格式如下：

def __setattr__(self, attr, val):

由于对所有的成员进行赋值操作时都调用这一个函数，所以一定要在该函数内部判断当前操作的属性名，也就是第二个参数。对于只读的属性，可以抛出异常；对于可写的属性，需要完成赋值操作。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def __init__(self, name, gender, age):    # 初始化函数
...         self.name = name
...         self.gender = gender
...         self._age = age
...     def __setattr__(self, attr, val):     # 定义 __setattr__()
...         if attr == 'age':                 # 如果是 age 属性，抛出异常
...             msg = '{}.{} is READ ONLY'.format(type(self).__name__, attr)
...             raise AttributeError(msg)
...         else:
...             self.__dict__[attr] = val     # 其他属性，可以修改
...                                           # 类定义结束
>>> student_a = Student('alex', 0, 18)        # 创建类实例对象
>>> student_a.age = 21                        # 修改 age 属性，抛出异常
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "<stdin>", line 11, in __setattr__
AttributeError: Student.age is READ ONLY

#### 类属性的安全检查

除了读写的检查，还可以进行更多的检查，如检查某个属性值的范围、属性值的类型检查等，这样可以保证用户提供的输入值是有效的，防止非法数据写入系统。

下面的例子演示了对年龄属性进行安全检查，不允许用户输入无效的年龄数据。

>>> class Student:
...     version = "1.0"
...     author = "python.cn"
...     def info():
...         print("version %s, author: %s" % (Student.version, Student.
        author))
...     def __init__(self, name, gender, age):
...         self.name = name
...         self.gender = gender
...         self._age = age
...     def get_age(self):
...         return self._age
...     def set_age(self, new_age):   # 对 age 属性进行写操作时的处理函数
...         if new_age > 40 or new_age < 14:
...             print("invalid age value")
...             return None
...         self._age = new_age
...         return new_age
...     age = property(get_age, set_age)
...                                    # 类定义结束
>>> student_a = Student('alex', 0, 18) # 创建实例对象
>>> student_a.age                      # 读取属性值
18
>>> student_a.age = 32                 # 设定属性
>>> student_a.age
32

另外一种用法如下。

>>> class Student:                 # 定义类
...     version = "1.0"
...     author = "python.cn"
...     def info():
...         print("version %s, author: %s" % (Student.version, Student.
        author))
...     def __init__(self, name, gender, age):
...         self.name = name
...         self.gender = gender
...         self._age = age
...     @property
...     def age(self):
...         return self._age
...     @age.setter                 # 定义 age 属性的设置函数
...     def age(self, val):
...         if val > 40 or val < 14:
...             print("Invalid age")
...             return self._age
...         self._age = val        # 类定义结束
>>> student_a = Student('alex', 0, 18)
>>> student_a.age
18
>>> student_a.age = 10
Invalid age
>>> student_a.age = 22
>>> student_a.age
22