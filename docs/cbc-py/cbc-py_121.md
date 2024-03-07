# Python namedtuple 工厂函数功能及用法

namedtuple() 是一个工厂函数，使用该函数可以创建一个 tuple 类的子类，该子类可以为 tuple 的每个元素都指定宇段名，这样程序就可以根据字段名来访问 namedtuple 的各元素了。当然，如果有需要，程序依然可以根据索引来访问 namedtuple 的各元素。

namedtuple 是轻量级的，性能很好，其并不比普通 tuple 需要更多的内存。namedtuple 函数的语法格式如下：

namedtuple(typename, field_names, *, verbose=False, rename=False, module=None)

关于该函数参数的说明如下；

*   typename：该参数指定所创建的 tuple 子类的类名，相当于用户定义了一个新类。
*   field_names：该参数是一个字符串序列，如 ['x'，'y']。此外，field_names 也可直接使用单个字符串代表所有字段名，多个字段名用空格、逗号隔开，如 'x y' 或 'x,y'。任何有效的 Python 标识符都可作为字段名（不能以下画线开头）。有效的标识符可由字母、数字、下画线组成，但不能以数字、下面线开头，也不能是关键字（如 return、global、pass、raise 等）。
*   rename：如果将该参数设为 True，那么无效的字段名将会被自动替换为位置名。例如指定 ['abc','def','ghi','abc']，它将会被替换为 ['abc', '_1','ghi','_3']，这是因为 def 字段名是关键字，而 abc 字段名重复了。
*   verbose：如果该参数被设为 True，那么当该子类被创建之后，该类定义就会被立即打印出来。
*   module：如果设置了该参数，那么该类将位于该模块下，因此该自定义类的 __module__ 属性将被设为该参数值。

下面程序示范了使用 namedtuple 工厂函数来创建命名元组：

```
from collections import namedtuple
# 定义命名元组类：Point
Point = namedtuple('Point', ['x', 'y'])
# 初始化 Point 对象，即可用位置参数，也可用命名参数
p = Point(11, y=22)
# 像普通元组一样用根据索引访问元素
print(p[0] + p[1]) # 33
# 执行元组解包，按元素的位置解包
a, b = p
print(a, b) # 11, 22
# 根据字段名访问各元素
print(p.x + p.y) # 33
print(p) # Point(x=11, y=22)
```

上面程序先创建了一个命名元组类 Point，它是 tuple 的子类，因此程序既可使用命名参数的方式设置元组成员，也可使用索引的方式设置元组成员。

在创建了 Point 对象之后，Point 就代表一个命名元组，程序既可使用普通元组的方式来访问元组的成员，也可使用字段名来访问元组的成员。

除上面介绍的用法之外，Python 还为命名元组提供了如下方法和属性：

*   _make(iterable)：类方法。该方法用于根据序列或可迭代对象创建命名元组对象。
*   _asdict()：将当前命名元组对象转换为 OrderedDict 字典。
*   _replace(**kwargs)：替换命名元组中一个或多个字段的值。
*   _source：该属性返回定义该命名元组的源代码。
*   _fields：该属性返回该命名元组中所有字段名组成的元组。

下面代码示范了上面方法和属性的用法：

```
my_data = ['East', 'North']
# 创建命名元组对象
p2 = Point._make(my_data)
print(p2) # Point(x='East', y='North')
# 将命名元组对象转换成 OrderedDict
print(p2._asdict()) # OrderedDict([('x', 'East'), ('y', 'North')])
# 替换命名元组对象的字段值
p2._replace(y='South')
print(p2) # Point(x='East', y='North')
# 输出 p2 包含的所有字段
print(p2._fields) # ('x', 'y')
# 再次定义一个命名元组类
Color = namedtuple('Color', 'red green blue')
# 再次定义命名元组类，其字段由 Point 的字段加上 Color 的字段
Pixel = namedtuple('Pixel', Point._fields + Color._fields)
# 创建 Pixel 对象，分别为 x、y、red、green、blue 字段赋值
pix = Pixel(11, 22, 128, 255, 0)
print(pix) # Pixel(x=11, y=22, red=128, green=255, blue=0)
```

运行上面程序， 可以看到如下输出结果：

33
11 22
33
Point(x=11, y=22)
Point(x='East', y='North')
OrderedDict([('x', 'East'), ('y', 'North')])
Point(x='East', y='North')
('x', 'y')
Pixel(x=11, y=22, red=128, green=255, blue=0)