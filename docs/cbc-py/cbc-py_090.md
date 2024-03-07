# Python __repr__()方法：显示属性

先看下面程序：

```
class Item:
    def __init__ (self, name, price):
        self.name = name
        self.price = price
# 创建一个 Item 对象，将之赋给 im 变量
im = Item('鼠标', 29.8)
# 打印 im 所引用的 Item 对象
print(im)
```

上面程序创建了一个 Item 对象，然后使用 print() 方法输出 Item 对象。编译、运行上面程序，将看到如下输出结果：

<__main__.Item object at 0x000001DB45D406D8>

当读者运行上面程序时，可能会看到不同的输出结果，at 后的 16 位十六进制数字可能发生改变。但这个输出结果是怎么来的呢？按道理来说，print() 函数只能在控制台打印字符串，而 Item 实例是内存中的一个对象，怎么能直接转换为字符串输出呢？

事实上，当使用该方法输出 Item 对象时，实际上输出的是 Item 对象的 __repr__() 方法的返回值。也就是说，下面两行代码的效果完全一样：

```
print (im)
print (im.__repr__)
```

__repr__() 是 Python 类中的一个特殊方法，由于 object 类己提供了该方法，而所有的 Python 类都是 object 类的子类，因此所有的 Python 对象都具有 __repr__() 方法。

因此，当程序需要将任何对象与字符串进行连接时，都可先调用 __repr__() 方法将对象转换成字符串，然后将两个字符串连接在一起。例如如下代码：

im str = im.__repr() + ""

__repr__() 是一个非常特殊的方法，它是一个“自我描述”的方法，该方法通常用于实现这样一个功能：当程序员直接打印该对象时，系统将会输出该对象的“自我描述”信息，用来告诉外界该对象具有的状态信息。

object 类提供的 __repr__() 方法总是返回该对象实现类的“类名+object at+内存地址”值，这个返回值并不能真正实现“自我描述”的功能，因此，如果用户需要自定义类能实现“自我描述”的功能，就必须重写 __repr__() 方法。例如下面程序：

```
class Apple:
    # 实现构造器
    def __init__(self, color, weight):
        self.color = color;
        self.weight = weight;
    # 重写 __repr__ 方法，用于实现 Apple 对象的“自我描述”
    def __repr__(self):
        return "Apple[color=" + self.color +\
            ", weight=" + str(self.weight) + "]"
a = Apple("红色" , 5.68)
# 打印 Apple 对象
print(a)
```

编译、运行上面程序，可以看到如下运行结果：

Apple[color=红色, weight=5.68]

从上面的运行结果可以看出，通过重写 Apple 类的 __repr__() 方法，就可以让系统在打印 Apple 对象时打印出该对象的“自我描述”信息。

大部分时候，重写 __repr__() 方法总是返回该对象的所有令人感兴趣的信息所组成的字符串。通常可返回如下格式的字符串：

类名[field1 = 值 1, field2 =值 2, ...]