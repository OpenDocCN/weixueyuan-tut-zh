# Python 类对象的创建和使用

创建对象的根本途径是构造方法，调用某个类的构造方法即可创建这个类的对象，Python 无须使用 new 调用构造方法。

前面章节中，我们已经创建了名为 Python 的类：

```
class Person :
    '这是一个学习 Python 定义的一个 Person 类'
    # 下面定义了一个类变量
    hair = 'black'
    def __init__(self, name = 'Charlie', age=8):
        # 下面为 Person 对象增加 2 个实例变量
        self.name = name
        self.age = age
    # 下面定义了一个 say 方法
    def say(self, content):
        print(content)
```

如下代码示范了调用 Person 类的构造方法：

```
# 调用 Person 类的构造方法，返回一个 Person 对象
# 将该 Person 对象赋给 p 变量
p = Person()
```

创建对象之后，接下来即可使用该对象了。Python 的对象大致有如下作用：

*   操作对象的实例变量（包括访问实例变量的值、添加实例变量、删除实例变量）。
*   调用对象的方法。

对象访问方法或变量的语法是：`对象.变量|方法（参数）`。在这种方式中，对象是主调者，用于访问该对象的变量或方法。

下面代码通过 Person 对象来调用 Person 的实例和方法：

```
# 输出 p 的 name、age 实例变量
print(p.name, p.age)  # Charlie 8
# 访问 p 的 name 实例变量，直接为该实例变量赋值
p.name = '李刚'
# 调用 p 的 say()方法，声明 say()方法时定义了 2 个形参
# 但第一个形参（self）是自动绑定的，因此调用该方法只需为第二个形参指定一个值
p.say('Python 语言很简单，学习很容易！')
# 再次输出 p 的 name、age 实例变量
print(p.name, p.age)  # 李刚 8
```

上面程序开始访问了 p 对象的 name、age 两个实例变量。这两个变量是何时定义的？留意在 Person 的构造方法中有如下两行代码：

self.name = name
self.age = age

这两行代码用传入的 name、age 参数（这两个参数都有默认值）对 self 的 name、age 变量赋值。由于 Python 的第一个 self 参数是自动绑定的（在构造方法中自动绑定到该构造方法初始化的对象），而这两行代码就是对 self 的 name、age 两个变量赋值，也就是对该构造方法初始化的对象（p 对象）的 name、age 变量赋值，即为 p 对象增加了 name、age 两个实例变量。

上面代码中通过 Person 对象调用了 say() 方法，在调用方法时必须为方法的形参赋值。但 say() 方法的第一个形参 self 是自动绑定的，它会被绑定到方法的调用者（p），因此程序只需要为 say() 方法传入一个字符串作为参数值，这个字符串将被传给 content 参数。

大部分时候，定义一个类就是为了重复创建该类的对象，同一个类的多个对象具有相同的特征，而类则定义了多个对象的共同特征。从某个角度来看，类定义的是多个对象的特征，因此类不是一个具体存在的实体，对象才是一个具体存在的实体。完全可以这样说：你不是人这个类，我也不是人这个类，我们都只是人的对象。

由于 Python 是动态语言，因此程序完全可以为 p 对象动态增加实例变量只要为它的新变量赋值即可；也可以动态删除实例变量（使用 del 语句即可删除）。例如如下代码：

```
# 为 p 对象增加一个 skills 实例变量
p.skills = ['programming', 'swimming']
print(p.skills)
# 删除 p 对象的 name 实例变量
del p.name
# 再次访问 p 的 name 实例变量
print(p.name)  # AttributeError
```

上面程序先为 p 对象动态增加了一个 skills 实例变量，即只要对 p 对象的 skills 实例变量赋值就是新增一个实例变量。接下来程序中调用 del 删除了 p 对象的 name 实例变量，当程序再次访问 print(p.name) 时就会导致 AttributeError 错误，并提示：`'Person' object has no attribute 'name'`。

## Python 对象动态添加变量和方法

Python 是动态语言，当然也允许为对象动态增加方法。比如上面程序中在定义 Person 类时只定义了一个 say() 方法，但程序完全可以为 p 对象动态增加方法。

但需要说明的是，为 p 对象动态增加的方法，Python 不会自动将调用者自动绑定到第一个参数（即使将第一个参数命名为 self 也没用）。例如如下代码：

```
# 先定义一个函数
def info(self):
    print("---info 函数---", self)
# 使用 info 对 p 的 foo 方法赋值（动态绑定方法）
p.foo = info
# Python 不会自动将调用者绑定到第一个参数，
# 因此程序需要手动将调用者绑定为第一个参数
p.foo(p)  # ①

# 使用 lambda 表达式为 p 对象的 bar 方法赋值（动态绑定方法）
p.bar = lambda self: print('--lambda 表达式--', self)
p.bar(p) # ②
```

上面的第 5 行和第 11 行代码分别使用函数、lambda 表达式为 p 对象动态增加了方法，但对于动态增加的方法，Python 不会自动将方法调用者绑定到它们的第一个参数，因此程序必须手动为第一个参数传入参数值，如上面程序中 ① 号、② 号代码所示。

如果希望动态增加的方法也能自动绑定到第一个参数，则可借助于 types 模块下的 MethodType 进行包装。例如如下代码：

```
def intro_func(self, content):
    print("我是一个人，信息为：%s" % content)
# 导入 MethodType
from types import MethodType
# 使用 MethodType 对 intro_func 进行包装，将该函数的第一个参数绑定为 p
p.intro = MethodType(intro_func, p)
# 第一个参数已经绑定了，无需传入
p.intro("生活在别处")
```

正如从上面代码所看到的，通过 MethodType 包装 intr_func 函数之后（包装时指定了将该函数的第一个参数绑定为 p），为 p 对象动态增加的 intro() 方法的第一个参数己经绑定，因此程序通过 p 调用 intro() 方法时无须传入第一个参数，就像定义类时己经定义了 intro() 方法一样。