# Python __call__ 方法（详解版）

前面章节中，我们用 hasattr() 函数判断指定属性（或方法）是否存在，但到底是属性还是方法，则需要进一步判断它是否可调用。程序可通过判断该属性（或方法）是否包含 __call__ 属性来确定它是否可调用。

例如如下程序：

```
class User:
    def __init__(self, name, passwd):
        self.name = name
        self.passwd = passwd
    def validLogin (self):
        print('验证%s 的登录' % self.name)       
u = User('crazyit', 'leegang')
# 判断 u.name 是否包含 __call__ 方法，即判断是否可调用
print(hasattr(u.name, '__call__')) # False
# 判断 u.passwd 是否包含 __call__ 方法，即判断是否可调用
print(hasattr(u.passwd, '__call__')) # False
# 判断 u.validLogin 是否包含 __call__ 方法，即判断是否可调用
print(hasattr(u.validLogin, '__call__')) # True
```

上面程序中粗体字代码分别判断 User 对象的 name、 passwd、 validLogin 是否包含 __call__ 方法，如果包含该方法，则表明它是可调用的，否则就说明它是不可调用的。

从上面程序的输出结果不难看到，对于 name、 passwd 两个属性，由于它们都是不可调用的，因此程序在判断它们是否包含 __call__ 方法时输出 False；对于 validLogin 方法，由于它是可调用的，因此程序在判断它是否包含 __call__  方法时输出 True。

实际上，一个函数（甚至对象）之所以能执行，关键就在于 __call__() 方法。实际上 x(arg1, arg2,...) 只是 x.__call__(arg1, arg2, ...) 的快捷写法，因此我们甚至可以为自定义类添加 __call__ 方法，从而使得该类的实例也变成可调用的。例如如下代码：

```
# 定义 Role 类
class Role:
    def __init__ (self, name):
        self.name = name
    # 定义 __call__ 方法
    def __call__(self):
        print('执行 Role 对象')
r = Role('管理员')
# 直接调用 Role 对象，就是调用该对象的 __call__ 方法
r()
```

上面程序中最后一行代码使用调用函数的语法来调用对象，这看上去似乎是错误的，但由于该 Role 类提供了 __call__ 方法，因此调用对象的本质就是执行该对象的 __call__ 方法。运行上面代码，将看到如下输出结果：

执行 Role 对象

对于程序中的函数，同样既可使用函数的语法来调用它，也可把函数当成对象，调用它的 __call__ 方法。例如如下示例代码：

```
def foo ():
    print('--foo 函数--')
# 下面示范了 2 种方式调用 foo()函数
foo()
foo.__call__()
```

运行上面代码，可以看到 foo() 和 foo.__call__() 的效果完全相同。