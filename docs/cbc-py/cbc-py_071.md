# Python property 函数：定义属性

如果为 Python 类定义了 getter、setter 等访问器方法，则可使用 property() 函数将它们定义成属性（相当于实例变量）。

property() 函数的语法格式如下：

property(fget=None, fset=None, fdel=None, doc=None)

从上面的语法格式可以看出，在使用 property() 函数时，可传入 4 个参数，分别代表 getter 方法、setter 方法、del 方法和 doc，其中 doc 是一个文档字符串，用于说明该属性。

当然，开发者调用 property 也可传入 0 个（既不能读，也不能写的属性）、1 个（只读属性）、2 个（读写属性）、3 个（读写属性，也可删除）和 4 个（读写属性，也可删除，包含文档说明）参数。

例如，如下程序定义了一个 Rectangle 类，该类使用 property() 函数定义了一个 size 属性：

```
class Rectangle:
    # 定义构造方法
    def __init__(self, width, height):
        self.width = width
        self.height = height
    # 定义 setsize()函数
    def setsize (self , size):
        self.width, self.height = size
    # 定义 getsize()函数
    def getsize (self):
        return self.width, self.height
     # 定义 getsize()函数
    def delsize (self):
        self.width, self.height = 0, 0 
    # 使用 property 定义属性
    size = property(getsize, setsize, delsize, '用于描述矩形大小的属性')
# 访问 size 属性的说明文档
print(Rectangle.size.__doc__)
# 通过内置的 help()函数查看 Rectangle.size 的说明文档
help(Rectangle.size)
rect = Rectangle(4, 3)
# 访问 rect 的 size 属性
print(rect.size) # (4, 3)
# 对 rect 的 size 属性赋值
rect.size = 9, 7
# 访问 rect 的 width、height 实例变量
print(rect.width) # 9
print(rect.height) # 7
# 删除 rect 的 size 属性
del rect.size
# 访问 rect 的 width、height 实例变量
print(rect.width) # 0
print(rect.height) # 0
```

程序中，使用 property() 函数定义了一个 size 属性，在定义该属性时一共传入了 4 个参数，这意味着该属性可读、可写、可删除，也有说明文档。所以，该程序尝试对 Rectangle 对象的 size 属性进行读、写、删除操作，其实这种读、写、删除操作分别被委托给 getsize()、setsize() 和 delsize() 方法来实现。

运行上面程序，将会看到如下输出结果：

用于描述矩形大小的属性
Help on property:

    用于描述矩形大小的属性

(4, 3)
9
7
0
0

在使用 property() 函数定义属性时，也可根据需要只传入少量的参数。例如，如下代码使用 property() 函数定义了一个读写属性，该属性不能删除：

```
class User :
    def __init__ (self, first, last):
        self.first = first
        self.last = last
    def getfullname(self):
        return self.first + ',' + self.last
    def setfullname(self, fullname):
        first_last = fullname.rsplit(',');
        self.first = first_last[0]
        self.last = first_last[1]
    # 使用 property()函数定义 fullname 属性，只传入 2 个参数
    # 该属性是一个读写属性，但不能删除
    fullname = property(getfullname, setfullname)
u = User('悟空', '孙')
# 访问 fullname 属性
print(u.fullname)
# 对 fullname 属性赋值
u.fullname = '八戒,朱'
print(u.first)
print(u.last)
```

此程序中使用 property() 定义了 fullname 属性，该程序使用 property() 函数时只传入两个参数，分别作为 getter 和 setter 方法，因此该属性是一个读写属性，不能删除。

运行上面程序，将看到如下输出结果：

悟空,孙
八戒
朱

在某些编程语言中，类似于这种 property 合成的属性被称为计算属性。这种属性并不真正存储任何状态，它的值其实是通过某种算法计算得到的。当程序对该属性赋值时，被赋的值也会被存储到其他实例变量中。

还可使用 ＠property 装饰器来修饰方法，使之成为属性。例如如下程序：

```
class Cell:
    # 使用@property 修饰方法，相当于为该属性设置 getter 方法
    @property
    def state(self):
        return self._state
    # 为 state 属性设置 setter 方法
    @state.setter
    def state(self, value):
        if 'alive' in value.lower():
            self._state = 'alive'
        else:
            self._state = 'dead'
    # 为 is_dead 属性设置 getter 方法
    # 只有 getter 方法属性是只读属性
    @property
    def is_dead(self):
        return not self._state.lower() == 'alive'
c = Cell()
# 修改 state 属性
c.state = 'Alive'
# 访问 state 属性
print(c.state)
# 访问 is_dead 属性
print(c.is_dead)
```

上面程序中第 3 行代码使用 ＠property 修饰了 state() 方法，这样就使得该方法变成了 state 属性的 getter 方法。如果只有该方法，那么 state 属性只是一个只读属性。

当程序使用 ＠property 修饰了 state 属性之后，又多出一个 ＠state.setter 装饰器，该装饰器用于修饰 state 属性的 setter 方法，如上面程序中第 7 行代码所示。这样 state 属性就有了 getter 和 setter 方法，state 属性就变成了读写属性。

程序中第 15 行代码使用 ＠property 修饰了 is_dead 方法，该方法就会变成 is_dead 属性的 getter 方法。此处同样会多出一个 ＠is_dead.setter 装饰器，但程序并未使用该装饰器修饰 setter 方法，因此 is_dead 属性只是一个只读属性。

运行上面程序，将看到如下输出结果：

alive
False