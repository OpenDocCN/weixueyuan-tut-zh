# Python 类调用实例方法

前面讲过，在 Python 的类体中定义的方法默认都是实例方法，也示范了通过对象来调用实例方法。

但要提醒大家的是，Python 的类在很大程度上是一个命名空间，当程序在类体中定义变量、方法时，与前面介绍的定义变量、定义函数其实并没有太大的不同。对比如下代码：

```
# 定义全局空间的 foo 函数
def foo ():
    print("全局空间的 foo 方法")
# 全局空间的 bar 变量
bar = 20
class Bird:
    # 定义 Bird 空间的 foo 函数
    def foo():
        print("Bird 空间的 foo 方法")
    # 定义 Bird 空间的 bar 变量
    bar = 200
# 调用全局空间的函数和变量
foo()
print(bar)
# 调用 Bird 空间的函数和变量
Bird.foo()
print(Bird.bar)
```

上面代码在全局空间和 Bird 类（Bird 空间）中分别定义了 foo() 函数和 bar 变量，从定义它们的代码来看，几乎没有任何区别，只是在 Bird 类中定义它们时需要缩进。

接下来程序在调用 Bird 空间内的 bar 变量和 foo() 函数（方法）时，只要添加 Bird. 前缀即可，这说明完全可以通过 Bird 类来调用 foo() 函数（方法）。这就是类调用实例方法的证明。

现在问题来了，如果使用类调用实例方法，那么该方法的第一个参数（self）怎么自动绑定呢？例如如下程序：

```
class User:
    def walk (self):
        print(self, '正在慢慢地走')
# 通过类调用实例方法
User.walk()
```

运行上面代码，程序会报出如下错误：

TypeError: walk() missing 1 required positional argument:'self'

请看程序最后一行代码，调用 walk() 方法缺少传入的 self 参数，所以导致程序出错。这说明在使用类调用实例方法时，Python 不会自动为第一个参数绑定调用者。实际上也没法自动绑定，因此实例方法的调用者是类本身，而不是对象。

如果程序依然希望使用类来调用实例方法，则必须手动为方法的第一个参数传入参数值。例如，将上面的粗体字代码改为如下形式：

```
u = User()
# 显式为方法的第一个参数绑定参数值
User.walk(u)
```

此代码显式地为 walk() 方法的第一个参数绑定了参数值，这样的调用效果完全等同于执行 u.walk()。

实际上，当通过 User 类调用 walk() 实例方法时，Python 只要求手动为第一个参数绑定参数值，并不要求必须绑定 User 对象，因此也可使用如下代码进行调用：

```
# 显式为方法的第一个参数绑定 fkit 字符串参数值
User.walk('fkit')
```

如果按上面方式进行绑定，那么 'fkit' 字符串就会被传给 walk() 方法的第一个参数 self。因此，运行上面代码，将会看到如下输出结果：

fkit 正在慢慢地走

## 总结

Python 的类可以调用实例方法，但使用类调用实例方法时，Python 不会自动为方法的第一个参数 self 绑定参数值；程序必须显式地为第一个参数 self 传入方法调用者。这种调用方式被称为“未绑定方法”。