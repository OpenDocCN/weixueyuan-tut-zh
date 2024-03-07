# Python 类属性和实例属性

> 原文：[`www.weixueyuan.net/a/649.html`](http://www.weixueyuan.net/a/649.html)

其实类本身也是一个对象，它也有自己的属性。类的属性一般表示类的相关内容，如类的版本和作者等信息。类的属性的定义是在类里面直接定义一个变量即可。

下面我们给类 Student 添加版本和作者信息。

>>> class Student:                             # 定义类 Student
...     version = "1.0"                 # 类的属性 version
...     author = "python.cn"    # 类的属性 author
...
>>> Student                                            # 查看类 Student
<class '__main__.Student'>
>>> Student.version                            # 查看类的属性
'1.0'
>>> Student.author                             # 查看类的属性
'python.cn'

可以看到，类的属性的访问方法是“类名。属性名”。这些属性也是可以修改的，如可以修改 Student 类的 version 属性。

>>> Student.version = "1.1"            # 修改类 Student 的属性 version
>>> Student.version                    # 查看修改后的类 Student 的属性 version 的值
'1.1'

那么我们能否定义类的函数呢？其实也可以的，如下面的代码中的 Student 类就有一个自己的类的函数 info()。

>>> class Student:                # 定义类 Student
...     version = "1.0"
...     author = "python.cn"
...     def info():               # 定义了类的函数
...         print("version %s, author: %s" % (Student.version,
        Student.author))
...
>>> hasattr(Student, "info")      # 是否存在 info 属性
True
>>> Student.info                  # 查看 info 属性
<function Student.info at 0x105cb09d8>
>>> Student.info()                # 使用 info 属性
version 1.0, author: python.cn

## 实例属性

Python 是面向对象的，即使是最简单的一个整数，它也是一个对象，因此也有自己的 ID 和属性。在本节中，对象特指我们用自定义的类创建的对象。

由于实例对象是基于类创建出来的，所以对象会继承类的属性。通过下面的例子可以看到对象自动继承了类的所有属性。

>>> class Student:                             # 定义类 Student
...     version = "1.0"
...     author = "python.cn"
...     def info():
...         print("version %s, author: %s" % (Student.version, Student.
author))
...
>>> student_a = Student()              # 创建对象 student_a
>>> student_a.version                  # student_a 的属性 version
'1.0'
>>> student_a.author                   # student_a 的属性 author
'python.cn'

需要注意的是，这些从类继承得到的对象，并不是复制下来的，它们是指向类的属性的。可以通过查看 id 的方法来观察这一点。

>>> id(student_a.version)     # 对象的某个从类继承的属性等于相应的类属性
48991624
>>> id(Student.version)
48991624
>>> student_a.version is Student.version    # 它们是同一个对象
True

还可以通过修改类属性来观察实例继承的属性也发生了变化。

>>> student_a.version                  # 对象属性
'1.0'
>>> Student.version = "1.1"            # 修改类属性
>>> student_a.version                  # 对象属性也发生了改变
'1.1'

但是修改对象的属性不会反映到类的属性上。如下面的例子中，修改了对象的属性 author，但是类 Student 的 author 属性是没有变化的。

>>> student_a.author = "inst_author"   # 修改对象的 author 属性
>>> student_a.author                   # 对象的 author 属性发生了改变
'inst_author'
>>> Student.author                     # 类的 author 属性没有发生变化
'python.cn'

比较特殊的情况是列表数据类型，它们可以在不改变 id 的情况下，发生内容的改变。如调用了 append() 接口函数，该列表的 id 不会发生变化，但其内容发生了变化。下面演示了这种情况。

>>> list_1 = []               # 构造一个列表
>>> list_1                    # 查看列表内容
[]
>>> id(list_1)                # 查看其 id
48545224
>>> list_1.append(2)          # 添加一个元素
>>> id(list_1)                # id 没有变化
48545224
>>> list_1                    # 但是内容发生了变化
[2]

对于这种类型的属性，如果 id 没有发生变化，修改对象从类继承来的属性会导致类属性的值的变化。如下面的代码所示。

>>> class Student:                           # 定义类 Student
...     years = []
...     def __init__(self):                  # 构造函数
...         print("__init__() is Running")
...     def __del__(self):                   # 销毁函数
...         print("__del__() is Running")
...     def append(self, element):           # 添加元素
...         self.years.append(element)
...     def change(self, list):              # 修改对象
...         self.years = list
...                                                                             # 类定义结束
>>> student_a = Student()      # 创建一个对象
__init__() is Running
>>> student_a.years
[]
>>> id(student_a.years)        # 可以发现对象属性和类属性有相同的 id
49089736
>>> id(Student.years)
49089736
>>> student_a.append(1)        # 修改 years 属性的内容，但是没有改变 id
>>> id(student_a.years)
49089736
>>> student_a.years            # 对象属性的内容改变了
[1]
>>> Student.years              # 类属性的内容也改变了
[1]

如果使用的是赋值等操作，则对象属性的 id 会发生改变，这样对象属性和类属性就没有关联了，和我们前面讲到的情况一致。下面是修改了对象的 id，也就是使用新的对象来表示该属性的情形。

>>> id(Student.years)       # 开始时对象属性和类属性是一个对象
49089736
>>> id(student_a.years)
49089736
>>> student_a.change([1,2,3])   # 重新赋值后对象属性的 id 发生了变化
>>> id(Student.years)           # 现在对象属性和类属性已经没有关联了
49089736
>>> id(student_a.years)
48699528