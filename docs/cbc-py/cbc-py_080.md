# Python issubclass 和 isinstance 函数：检查类型

Python 提供了如下两个函数来检查类型：

*   issubclass(cls, class_or_tuple)：检查 cls 是否为后一个类或元组包含的多个类中任意类的子类。
*   isinstance(obj, class_or_tuple)：检查 obj 是否为后一个类或元组包含的多个类中任意类的对象。

通过使用上面两个函数，程序可以方便地先执行检查，然后才调用方法，这样可以保证程序不会出现意外情况。

如下程序示范了通过这两个函数来检查类型：

```
# 定义一个字符串
hello = "Hello";
# "Hello"是 str 类的实例，输出 True
print('"Hello"是否是 str 类的实例: ', isinstance(hello, str))
# "Hello"是 object 类的子类的实例，输出 True
print('"Hello"是否是 object 类的实例: ', isinstance(hello, object))
# str 是 object 类的子类，输出 True
print('str 是否是 object 类的子类: ', issubclass(str, object))
# "Hello"不是 tuple 类及其子类的实例，输出 False
print('"Hello"是否是 tuple 类的实例: ', isinstance(hello, tuple))
# str 不是 tuple 类的子类，输出 False
print('str 是否是 tuple 类的子类: ', issubclass(str, tuple))
# 定义一个列表
my_list = [2, 4]
# [2, 4]是 list 类的实例，输出 True
print('[2, 4]是否是 list 类的实例: ', isinstance(my_list, list))
# [2, 4]是 object 类的子类的实例，输出 True
print('[2, 4]是否是 object 类及其子类的实例: ', isinstance(my_list, object))
# list 是 object 类的子类，输出 True
print('list 是否是 object 类的子类: ', issubclass(list, object))
# [2, 4]不是 tuple 类及其子类的实例，输出 False
print('[2, 4]是否是 tuple 类及其子类的实例: ', isinstance([2, 4], tuple))
# list 不是 tuple 类的子类，输出 False
print('list 是否是 tuple 类的子类: ', issubclass(list, tuple))
```

通过上面程序可以看出，issubclass() 和 isinstance() 两个函数的用法差不多，区别只是 issubclass() 的第一个参数是类名，而 isinstance() 的第一个参数是变量，这也与两个函数的意义对应：issubclass 用于判断是否为子类，而 isinstance() 用于判断是否为该类或子类的实例。

issubclass() 和 isinstance() 两个函数的第二个参数都可使用元组。例如如下代码：

```
data = (20, 'fkit')
print('data 是否为列表或元组: ', isinstance(data, (list, tuple))) # True
# str 不是 list 或者 tuple 的子类，输出 False
print('str 是否为 list 或 tuple 的子类: ', issubclass(str, (list, tuple)))
# str 是 list 或 tuple 或 object 的子类，输出 True
print('str 是否为 list 或 tuple 或 object 的子类 ', issubclass(str, (list, tuple, object)))
```

此外，Python 为所有类都提供了一个 __bases__ 属性，通过该属性可以查看该类的所有直接父类，该属性返回所有直接父类组成的元组。例如如下代码：

```
class A:
    pass
class B:
    pass
class C(A, B):
    pass
print('类 A 的所有父类:', A.__bases__)
print('类 B 的所有父类:', B.__bases__)
print('类 C 的所有父类:', C.__bases__)
```

运行上面程序，可以看到如下运行结果：

类 A 的所有父类: (<class 'object'>,)
类 B 的所有父类: (<class 'object'>,)
类 C 的所有父类: (<class '__main__.A'>, <class '__main__.B'>)

从上面的运行结果可以看出，如果在定义类时没有显式指定它的父类，则这些类默认的父类是 object 类。

Python 还为所有类都提供了一个 __subclasses__() 方法，通过该方法可以查看该类的所有直接子类，该方法返回该类的所有子类组成的列表。例如在上面程序中增加如下两行：

```
print('类 A 的所有子类:', A.__subclasses__())
print('类 B 的所有子类:', B.__subclasses__())
```

运行上面代码，可以看到如下输出结果：

类 A 的所有子类: [<class '__main__.C'>]
类 B 的所有子类: [<class '__main__.C'>]