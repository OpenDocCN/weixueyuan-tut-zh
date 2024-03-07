# Python 枚举类定义和使用（详解版）

在某些情况下，一个类的对象是有限且固定的，比如季节类，它只有 4 个对象；再比如行星类，目前只有 8 个对象。这种实例有限且固定的类，在 Python 中被称为枚举类。

程序有两种方式来定义枚举类：

1.  直接使用 Enum 列出多个枚举值来创建枚举类。
2.  通过继承 Enum 基类来派生枚举类。

如下程序示范了直接使用 Enum 列出多个枚举值来创建枚举类：

```
import enum
# 定义 Season 枚举类
Season = enum.Enum('Season', ('SPRING', 'SUMMER', 'FALL', 'WINTER'))
```

上面程序使用 Enum() 函数（就是 Enum 的构造方法）来创建枚举类，该构造方法的第一个参数是枚举类的类名；第二个参数是一个元组，用于列出所有枚举值。

在定义了上面的 Season 枚举类之后，程序可直接通过枚举值进行前问，这些枚举值都是该枚举的成员，每个成员都有 name、value 两个属性，其中 name 属性值为该枚举值的变量名，value 代表该枚举值的序号（序号通常从 1 开始）。

例如，如下代码测试了枚举成员的用法：

```
# 直接访问指定枚举
print(Season.SPRING)
# 访问枚举成员的变量名
print(Season.SPRING.name)
# 访问枚举成员的值
print(Season.SPRING.value)
```

运行该程序，可以看到如下输出结果：

Season.SPRING
SPRING
1

程序除可直接使用枚举之外，还可通过枚举变量名或枚举值来访问指定枚举对象。例如如下代码：

```
# 根据枚举变量名访问枚举对象
print(Season['SUMMER']) # Season.SUMMER
# 根据枚举值访问枚举对象
print(Season(3)) # Season.FALL
```

此外，Python 还为枚举提供了一个 __members__ 属性，该属性返回一个 dict 字典，字典包含了该枚举的所有枚举实例。程序可通过遍历 __members__ 属性来访问枚举的所有实例。例如如下代码：

```
# 遍历 Season 枚举的所有成员
for name, member in Season.__members__.items():
    print(name, '=>', member, ',', member.value)
```

运行上面代码，可以看到如下输出结果：

SPRING => Season.SPRING , 1
SUMMER => Season.SUMMER , 2
FALL => Season.FALL , 3
WINTER => Season.WINTER , 4

如果要定义更复杂的枚举，则可通过继承 Enum 来派生枚举类，在这种方式下程序就可以为枚举额外定义方法了。例如如下程序：

```
import enum
class Orientation(enum.Enum):
    # 为序列值指定 value 值
    EAST = '东'
    SOUTH = '南'
    WEST = '西'
    NORTH = '北'
    def info(self):
        print('这是一个代表方向【%s】的枚举' % self.value)
print(Orientation.SOUTH)
print(Orientation.SOUTH.value)
# 通过枚举变量名访问枚举
print(Orientation['WEST'])
# 通过枚举值来访问枚举
print(Orientation('南'))
# 调用枚举的 info()方法
Orientation.EAST.info()
# 遍历 Orientation 枚举的所有成员
for name, member in Orientation.__members__.items():
    print(name, '=>', member, ',', member.value)
```

上面程序通过继承 Enum 派生了 Orientation 枚举类，通过这种方式派生的枚举类既可额外定义方法，如上面的 info() 方法所示，也可为枚举指定 value（value 的值默认是 1、2、3、…）。

虽然此时 Orientation 枚举的 value 是由类型，但该枚举同样可通过 value 来访问特定枚举，如上面程序中的 Orientation('南')，这是完全允许的。运行上面代码，可以看到如下输出结果：

Orientation.SOUTH
南
Orientation.WEST
Orientation.SOUTH
这是一个代表方向【东】的枚举
EAST => Orientation.EAST , 东
SOUTH => Orientation.SOUTH , 南
WEST => Orientation.WEST , 西
NORTH => Orientation.NORTH , 北

## 枚举的构造器

枚举也是类，因此枚举也可以定义构造器。为枚举定义构造器之后，在定义枚举实例时必须为构造器参数设置值。例如如下程序：

```
import enum
class Gender(enum.Enum):
    MALE = '男', '阳刚之力'
    FEMALE = '女', '柔顺之美'
    def __init__(self, cn_name, desc):
        self._cn_name = cn_name
        self._desc = desc
    @property
    def desc(self):
        return self._desc
    @property
    def cn_name(self):
        return self._cn_name
# 访问 FEMALE 的 name
print('FEMALE 的 name:', Gender.FEMALE.name)
# 访问 FEMALE 的 value
print('FEMALE 的 value:', Gender.FEMALE.value)
# 访问自定义的 cn_name 属性
print('FEMALE 的 cn_name:', Gender.FEMALE.cn_name)
# 访问自定义的 desc 属性
print('FEMALE 的 desc:', Gender.FEMALE.desc)
```

上面程序定义了 Gender 枚举类，并为它定义了一个构造器，调用该构造器需要传入 cn_name 和 desc 两个参数，因此程序使用如下代码来定义 Gender 的枚举值。

MALE = '男', '阳刚之力'
FEMALE = '女', '柔顺之美'

上面代码为 MALE 枚举指定的 value 是‘男’和‘阳刚之力’这两个字符串，其实它们会被自动封装成元组后传给 MALE 的 value 属性；而且此处传入的‘男’和‘阳刚之力’ 这两个参数值正好分别传给 cnname 和 desc 两个参数。简单来说，枚举的构造器需要几个参数，此处就必须指定几个值。

运行上面程序，可以看到如下输出结果：

FEMALE 的 name: FEMALE
FEMALE 的 value: ('女', '柔顺之美')
FEMALE 的 cn_name: 女
FEMALE 的 desc: 柔顺之美