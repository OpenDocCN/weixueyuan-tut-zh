# Python setattr()、getattr()、hasattr()函数用法详解

在动态检查对象是否包含某些属性（包括方法〉相关的函数有如下几个：

1.  hasattr(obj, name)：检查 obj 对象是否包含名为 name 的属性或方法。
2.  getattr(object, name[, default])：获取 object 对象中名为 name 的属性的属性值。
3.  setattr(obj, name, value，/)：将 obj 对象的 name 属性设为 value。

下面程序示范了通过以上函数来动态操作 Python 对象的属性：

```
class Comment:
    def __init__ (self, detail, view_times):
        self.detail = detail
        self.view_times = view_times
    def info ():
        print("一条简单的评论，内容是%s" % self.detail)

c = Comment('疯狂 Python 讲义很不错', 20)
# 判断是否包含指定的属性或方法
print(hasattr(c, 'detail')) # True
print(hasattr(c, 'view_times')) # True
print(hasattr(c, 'info')) # True
# 获取指定属性的属性值
print(getattr(c, 'detail')) # '疯狂 Python 讲义很不错'
print(getattr(c, 'view_times')) # 20
# 由于 info 是方法，故下面代码会提示：name 'info' is not defined
#print(getattr(c, info, '默认值'))
# 为指定属性设置属性值
setattr(c, 'detail', '天气不错')
setattr(c, 'view_times', 32)
# 输出重新设置后的属性值
print(c.detail)
print(c.view_times)
```

上面程序先定义了一个 Comment 类，接下来程序创建了 Comment 类的实例。程序后面部分示范了 hasattr() 、getattr() 、setatttr() 三个函数的用法。

从上面的示例代码可以看出，hasattr() 函数既可判断属性，也可判断方法，但 getattr() 则只能获取属性的属性值。

运行上面代码，可以看到如下输出结果：

True
True
True
疯狂 Python 讲义很不错
20
天气不错
32

从上面最后两行输出来看，程序使用 setattr() 函数可改变 Python 对象的属性值；如果使用该函数对 Python 对象设置的属性不存在，那么就表现为添加属性反正 Python 是动态语言。看如下代码：

```
#设置不存在的属性，即为对象添加属性
setattr(c, 'test', '新增的测试属性')
print(c.test) # 新增的测试属性
```

实际上 setattr() 函数还可对方法进行设置，在使用 setattr() 函数重新设置对象的方法时， 新设置的方法是未绑定方法。例如如下代码：

```
def bar ():
    print('一个简单的 bar 方法')
# 将 c 的 info 方法设为 bar 函数   
setattr(c, 'info', bar)
c.info()
```

上面程序先定义了一个 bar() 函数，在该函数中不能定义 self 参数（否则需要程序员显式为参数传入参数值，系统不会自动为该参数绑定参数值）。接下来程序调用 setattr() 函数将 Comment 对象的 info() 方法设置为 bar() 函数，然后程序调用 Comment 对象的 info() 方法，其实就是调用程序中的 bar() 函数。

运行上面代码，可以看到运行 c.info() 方法时实际执行的是前面定义的 bar() 函数。

不仅如此，程序完全可通过 setattr() 函数将 info() 方法设置成普通值，这样将会把 info 变成一个属性，而不是方法，例如如下代码：

```
# 将 c 的 info 设置为字符串'fkit'
setattr(c, 'info', 'fkit')
c.info()
```

上面第一行代码将 c 的 info（原本是方法）设置为字符串 'fkit'，这样 info 就变成了属性，而不是方法。运行上面第二行代码，将会看到如下错误提示：

TypeError：'str' object is not callable