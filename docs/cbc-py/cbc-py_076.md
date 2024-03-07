# Python __slots__：限制类实例动态添加属性和方法

前面介绍了为对象动态添加方法，但是所添加的方法只是对当前对象有效，如果希望为所有实例都添加方法，则可通过为类添加方法来实现。例如如下代码：

```
class Cat:
    def __init__(self, name):
        self.name = name
def walk_func(self):
    print('%s 慢慢地走过一片草地' % self.name)
d1 = Cat('Garfield')
d2 = Cat('Kitty')
#d1.walk() # AttributeError
# 为 Cat 动态添加 walk()方法，该方法的第一个参数会自动绑定
Cat.walk = walk_func  # ①
# d1、d2 调用 walk()方法
d1.walk()
d2.walk()
```

上面程序定义了一个 Cat 类，该 Cat 类只定义了一个构造方法，并未提供任何方法。因此，程序第 8 行代码调用 d1.walk() 方法时会出现异常，Cat 类并没有 walk() 方法。

程序中 ① 号代码为 Cat 动态添加了 walk() 方法，为类动态添加方法时不需要使用 MethodType 进行包装，该函数的第一个参数会自动绑定。为 Cat 动态添加 walk() 方法之后，Cat 类的两个实例 d1、d2 都具有了 walk() 方法，因此上面程序中最后两行 d1、d2 都可调用 walk() 方法。

Python 的这种动态性固然有其优势，但也给程序带来了一定的隐患，即程序定义好的类，完全有可能在后面被其他程序修改，这就带来了一些不确定性。如果程序要限制为某个类动态添加属性和方法，则可通过 __slots__ 属性来指定。

__slots__ 属性的值是一个元组，该元组的所有元素列出了该类的实例允许动态添加的所有属性名和方法名（对于 Python 而言，方法相当于属性值为函数的属性）。例如如下程序：

```
class Dog:
    __slots__ = ('walk', 'age', 'name')
    def __init__(self, name):
        self.name = name
    def test():
        print('预先定义的 test 方法')
d = Dog('Snoopy')
from types import MethodType
# 只允许动态为实例添加 walk、age、name 这 3 个属性或方法
d.walk = MethodType(lambda self: print('%s 正在慢慢地走' % self.name), d)
d.age = 5
d.walk()
d.foo = 30 # AttributeError
```

上面程序中第 2 行代码定义了 __slots__=('walk','age', 'name')，这意味着程序只允许为 Dog 实例动态添加 walk、age、name 这三个属性或方法。因此上面程序中第 10 行、第 11 行代码为 Dog 对象动态添加 walk() 方法和 age 属性都是允许的。

但如果程序尝试为 Dog 对象添加其他额外属性，程序就会引发 AttributeError 错误，如上面最后一行代码所示。运行上面程序，可以看到如下输出结果：

Snoopy 正在慢慢地走
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 13, in <module>
    d.foo = 30 # AttributeError
AttributeError: 'Dog' object has no attribute 'foo'

需要说明的是，__slots__ 属性并不限制通过类来动态添加属性或方法，因此下面代码是合法的：

```
# __slots__ 属性并不限制通过类来动态添加方法
Dog.bar = lambda self: print('abc') # AttributeError
d.bar()
```

下面代码通过 Dog 类动态添加了 bar() 方法，这样 Dog 对象就可以调用该 bar() 方法了。

此外，__slots__ 属性指定的限制只对当前类的实例起作用，对该类派生出来的子类是不起作用的。例如如下代码：

```
class GunDog(Dog):
    def __init__(self, name):
        super().__init__(name)
    pass
gd = GunDog('Puppy')
# 完全可以为 Gundog 实例动态添加属性
gd.speed = 99
print(gd.speed)
```

正如从上面代码所看到的，Dog 的子类 GunDog 的实例完全可以动态添加 speed 属性，这说明 __slots__ 属性指定的限制只对当前类起作用。

如果要限制子类的实例动态添加属性和方法，则需要在子类中也定义 __slots__ 属性，这样，子类的实例允许动态添加属性和方法就是子类的 __slots__ 元组加上父类的 __slots__ 元组的和。