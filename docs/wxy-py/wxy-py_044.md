# Python 常用函数汇总

> 原文：[`www.weixueyuan.net/a/610.html`](http://www.weixueyuan.net/a/610.html)

本节介绍和数据对象有关的函数，如得到某个对象的 id 值，判断这两个对象是否为同一个对象等。

## 与数据对象有关的函数

#### 1、Python id()：得到某个对象的标识

id() 函数返回某个对象的标识。如果是在 CPython 中，其返回的是该对象在内存中的地址。如果两个对象的标识相同，则这两个对象是同一个对象；如果不同，则为不同的对象。

```

>>> a = [1, 2, 3, 4]                    # 新建列表对象 a
>>> id(a)                               # 查看 a 的标识
4504821640
>>> b = a                               # b 也指向 a 所指向的对象
>>> id(b)                               # 查看 b 的标识
4504821640                              # 发现 a 和 b 的标识是相等的
>>> a is b                              # 判断对象 a 和对象 b 是否是同一个对象
True                                    # 它们是同一个对象
>>> a == b                              # 它们的值也应该相等
True
>>> a.append("new element")             # 修改 a，给它加上一个新的元素
>>> a                                   # 查看 a 的内容
[1, 2, 3, 4, 'new element']             # 可以看到这个新的元素
>>> b                                   # b 的值是否也会发生改变
[1, 2, 3, 4, 'new element']             # 是的，b 的值和 a 的值相同，它们是同一个对象
```

#### 2、Python help()：得到某个函数或者类的帮助信息

help() 函数的作用和 Linux 下的 man 类似。如果不知道某个函数怎么使用，可以通过该函数得到其相关的帮助信息。

```

>>> help(print)                                # 得到 print()函数的帮助信息
Help on built-in function print in module builtins:     # 帮助信息
print(...)
print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)
Prints the values to a stream, or to sys.stdout by default.
Optional keyword arguments:
file:  a file-like object (stream); defaults to the current sys.
stdout.
sep:   string inserted between values, default a space.
end:   string appended after the last value, default a newline.
flush: whether to forcibly flush the stream.
```

#### 3、Python dir()：得到对象的成员列表

如果带有输入对象，那么就返回该对象所有属性和函数名称的列表，该列表按照名称排序；如果没有输入参数，那么就返回当前范围内所有定义的符号。

```

>>> a = [1, 2, 3]                      # 构造列表对象 a
>>> dir(a)                                     # 显示 a 的属性和函数名称列表，自动排序
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__',
'__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
'__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '
__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__',
'__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
'__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__',
'__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count',
'extend',
'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
>>> dir()                                      # 显示当前的符号列表
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__',
'__package__', '__spec__', 'a', 'b', 'get_info', 'get_max_2', 'no_ret_
demo']
```

#### 4、Python type()：得到对象类型

type() 函数在前面已经使用过了。需要注意的是，在 Python 2 和 Python 3 中，该函数的返回值是不同的。

如在 Python 2 中，输出如下： 

```

>>> type("")
<type 'str'>
```

在 Python 3 中，输出如下：

```

>>> type("")
<class 'str'>
```

但不论是在 Python 2 中还是在 Python 3 中，下面的判断方法都不会出现问题。

```

>>> a= "abc"
>>> type(a) is str
True
```

#### 5、Python isinstance（对象，类型）

isinstance() 函数用于判断某个对象是否属于指定的类型。

```

>>> a = True
>>> isinstance(a, bool)     # a 是布尔型的
True
>>> isinstance(a, int)      # a 也是整型的
True
>>> isinstance(a, str)      # a 不是字符串型的
False
```

#### 6、Python eval()：将字符串转换成对象

如果从某个文本文件中读出一些字符串，其格式符合 Python 的语法，那么可以使用 eval( ) 函数将其转换为 Python 的对象。下面的例子是将字符串转换为长列表。

```

>>> a = "[1, 2, 3]"   # 输入字符串，格式符合 Python 列表的格式
>>> b = eval(a)       # 将值转换成 Python 对象
>>> type(b)           # 查看该对象的类型，是一个列表
<class 'list'>
>>> b                 # 查看该对象的值
[1, 2, 3]
```

#### 7、Python del()：删除对象

如果不需要某个对象了，可以使用 del()函数将其回收。

```

>>> a = 12        # 定义对象 a
>>> a             # a 是存在的
12
>>> del(a)        # 删除对象 a
>>> a             # 现在 a 是不存在的
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
NameError: name 'a' is not defined
```

在 Python 中一切都是对象，函数也是对象。下面的例子中定义了一个函数，然后删除该函数对象，之后该函数便不可用了。

```

>>> def func_a():        # 定义函数 func_a()
print("hello python")
# 函数定义结束
>>> func_a               # 查看函数对象 func_a()
<function func_a at 0x109d33ae8>
>>> func_a()             # 调用函数 func_a()
hello python
>>> del(func_a)          # 删除对象 func_a()
>>> func_a               # 该对象不存在了
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
NameError: name 'func_a' is not defined
>>> func_a()             # 调用该函数，失败
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
NameError: name 'func_a' is not defined
>>> id(func_a)           # 查看该函数的地址，失败
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
NameError: name 'func_a' is not defined
```

## 数学计算有关的函数

#### 1、Python abs()求绝对值

abs() 函数用来得到某个数值的绝对值。

```

>>> abs(12)    # 得到整数 12 的绝对值
12
>>> abs(-12)   # 得到整数-12 的绝对值
12
>>> abs(12.4)  # 得到浮点数 12.4 的绝对值
12.4
>>> abs(-12.4) # 得到浮点数-12.4 的绝对值
12.4
```

如果输入的是复数，那么得到的是其模。

```

>>> abs(12.4+3j)                       # 对复数 12.4+3j 取模
12.75774274705365# 模是 12.7
12.75774274705365>>> abs(12.4-3j)      # 对复数 12.4-3j 取模
# 和前面的值应该相等
>>> abs(-12.4-3j) )                    # 对复数-12.4-3j 取模
12.75774274705365 # 和前面的值应该相等
>>> abs(-12.4+3j) )                    # 对复数-12.4+3j 取模
12.75774274705365 
```

#### 2、Python sum()：求和

sum()函数对输入的序列求和，并返回这个和。对于前面的高斯求和问题，也可以直接使用该函数来完成。下面是实现代码：

```

>>> sum(range(1, 101))                 # 对 1 到 100 的序列求和
5050
```

#### 3、Python max()：求最大值

max() 函数用于求列表或者元组中所有元素的最大值。下面是求列表中最大值的例子。

```

>>> a = [1, 2, 3,21, 99, 21]
>>> max(a)
99
```

下面是求元组中最大值的例子。

```

>>> a = (1, 2, 3,21, 99, 21)
>>> max(a)
99
```

如果输入的是字典，则求键的最大值。

```

>>> a = {12:1, 999:2, 2:3}   # 键列表是[12, 999, 2]
>>> max(a)                   # 最大值是 999
999
```

对于不同类型的元素，如果都是数值类型，也可以直接标大小。在下面的例子中，元素有整数和浮点数，但它们都是数值类型，是可以比较大小的。

```

>>> a = [12, 23, 99.7]
>>> max(a)
99.7
>>> a = [212, 23, 99.7]
>>> max(a)
212
```

字符串之间也可以比较大小，其大小确实是按照字典顺序来排列的。下面的例子是求字符串列表中的最大值。

```

>>> a = ['a', 'b', 'c']
>>> max(a)
'c'
```

但对于既包含字符串类型的元素，也包含整数类型的元素的列表，默认比较方法就不好使了。下面的例子就是求既有字符串又有整数的列表的最大值。

```

>>> a = [100, 200, "300"]              # 在 Python 3 中的运行情况
>>> max(a)                             # 抛出异常
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: '>' not supported between instances of 'str' and 'int'
```

可见，在 Python 2 中，没有抛出异常。注意下面的代码的运行情况：

```

>>> a = [100, 200, "300"]       # 在 Python 2 中的运行情况
>>> max(a)                      # 认为'300'是最大的元素
'300'
```

但这个潜在转换有时也会让人发晕，不知道它是如何计算出来的。因此最好的办法是自己定义一个比较函数，这样元素之间的大小关系就由自己来确定了。

另外需要注意的是，如果输入集合中元素为 0，如空列表，那么会抛出 ValueError 异常。

```

>>> min([])                        # 抛出异常
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ValueError: min() arg is an empty sequence
```

#### 4、Python min()：求最小值

min() 函数返回集合元素中的最小值。

```

a = [1, 2, 3]
>>> min(a)
1
>>> d = [10, 1.3, 12.0]
>>> min(d)
1.3
```

其他需要注意的事项和 max() 一样，如在 Python 3 中，输入不能包含字符串元素。但在 Python 2 中，如果元素不是数值型的，则该元素会被忽略。

## 容器类型有关的函数

#### 1、Python len()：得到元素个数

len() 函数返回一个整数值，表示输入参数包含的元素个数。如果输入的是字符串，那么表示该字符串包含的字符个数。

```

>>> len("hello")          # 字符串"hello"包含的字符个数
5                         # 包含 5 个字符
>>> len("")               # 空字符串" "包含的字符个数
0                         # 包含 0 个字符
```

如果是列表、元组、字典和集合，则返回它们中的成员个数。

```

>>> len((1, 2, 3))            # 得到元组的元素个数
3
>>> len([1, 2, 3])            # 得到列表的元素个数
3
>>> len({"name": "alex", "age": 26})   # 得到字典的元素个数
2
>>> a = set([1, 2, 3])      # 构建集合 a
>>> len(a)                  # 得到集合对象 a 的元素个数
3
```

#### 2、Python sorted()：排序

sorted()函数对输入进行排序，并生成一个新的列表。输入的对象在操作过程中不会发生变化。

```

>>> sorted([1, 3, 10, 2, 4, 5])   # 输入是列表
[1, 2, 3, 4, 5, 10]               # 输出是排好序的列表
>>> sorted((1, 3, 10, 2, 4, 5))   # 输入是元组
[1, 2, 3, 4, 5, 10]
```

#### 3、Python reversed()：倒序

reversed() 函数返回某个序列的倒序遍历迭代器，使用该迭代器会按照从后向前的顺序来依次访问全部元素。

```

>>> a = [1, 2, 3]           # 创建列表对象 a
>>> b = reversed(a)         # 得到 a 的反向遍历迭代器
>>> b                       # 查看 b 的类型
<list_reverseiterator object at 0x10cd9ef98>
>>> for x in a:             # 从头到尾遍历 a
... print(x)
1
2
3
# 遍历的输出
>>> for x in b:             # 从尾到头遍历 a
... print(x)                # 遍历输出
3
2
1
```

#### 4、Python all()：全部元素为真

如果所有的元素为 True，那么其返回值为 True；否则返回值为 False。

```

>>> all([True, True, True])     # 所有的元素为 True，返回值为 True
True
>>> all([True, True, False])    # 所有的元素为 False，返回值为 False
False
```

如果没有元素，返回 True。

```

>>> all([])                     # 列表，没有元素
True
```

如果元素类型不为布尔型，那么是否可以使用该函数做判断呢？答案是可以的。这时可以使用“bool（对象）”的方法来判断其是否为真，如空的列表为 False，不为 0 的整数表示 True。

```

>>> bool([])          # 空的列表可以认为是 False
False
>>> bool([0])         # 不空的列表可以认为是 True
True
>>> bool(0)           # 整数 0 看作是 False
False
>>> bool(0.000001)    # 非 0 浮点数被看作是 True
True
```

参照上面的方法，输入包含的元素也可以不是布尔型的，但会被自动转换成布尔型来进行判断。

```

>>> all([1, 1, 10])   # 非 0 整数都被看作是 True
True
>>> all([1, 1, 12])
True
>>> all([1, 1, 12, 0])  # 整数 0 被看作是 False
False
```

#### 5、Python any()：包含真的元素

如果有元素为 True，那么其返回值是 True；否则返回值是 False。判断元素是否为 True 的方法和 all() 一样。

```

>>> a = [1, 0, 0]    # 第一个元素为 True，所以 any 返回 True
>>> any(a)
True
>>> b = ["", 0, 0.0, None, []] # 所有元素都为 False，所以 any 返回 False
>>> any(b)
False
```

#### 6、Python range(start,stop,step)：序列

在 Python 2 中，range(start,stop,step) 函数返回一个列表。

```

>>> range(10)      # 在 Python 2 中产生一个列表
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

而在 Python 3 中，range(start,stop,step) 函数返回一个对象。

```

>>> r = range(10)      # 产生一个 range 对象
>>> type(r)            # 返回值的类型是 range 类的对象
<class 'range'>
>>> r.start
0
>>> r.step        # 步长
1
>>> r.stop       # 结束值，不包括在内
10
```

Python 3 的做法是一种惰性求值，它并不会一次性将所有的列表元素都生成，它会在使用时才生成。这样做的好处是占用的资源相对来说要少一些。当然我们可以将其转换成列表，这样就和 Python 2 一样了。

```

>>> r = range(10)             # 生成一个 range 对象
>>> list(r)                   # 将值转换成列表
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

前面只使用了一个参数 stop，生成从 0 到 stop-1 的序列。我们也可以使用两个参数，start 和 stop，生成从 start 到 stop-1 的序列。下面就是该用法的例子。

```

>>> r = range(3, 10)    # start=3, stop=10
>>> list(r)             # 步长为默认值 1
[3, 4, 5, 6, 7, 8, 9]
```

我们也可以指定步长。如希望得到 100 以内的正偶数序列，可以使用下面的方法获得。

```

>>> r = range(2, 100, 2)  # 从 2 开始，小于 100，步长是 2
>>> list(r)               # 转换成列表
[2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24,    # 列表内容
26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46,
48, 50, 52, 54, 56, 58, 60, 62, 64, 66, 68,
70, 72, 74, 76, 78, 80, 82, 84, 86, 88, 90,
92, 94, 96, 98]
```

类似地，也可以得到 100 以内的正奇数列表，如下面的代码所示。

```

>>> r = range(1, 100, 2)   # 从 1 开始，小于 100，步长为 2
>>> list(r)                # 转换成列表
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23,     # 列表内容
25, 27, 29, 31, 33, 35, 37, 39, 41, 43, 45,
47, 49, 51, 53, 55, 57, 59, 61, 63, 65, 67,
69, 71, 73, 75, 77, 79, 81, 83, 85, 87,
89, 91, 93, 95, 97, 99]
```

下面通过一个例子——高斯求和问题来说明 range() 函数的使用。德国大数学家高斯小时候就喜欢动脑筋，上小学三年级时老师出了一道题，求 1+2+3+4+5+…+99+100 的和。这个题本身不难，但是需要进行 99 次加法运算，所以小朋友在短时间内都没有完成。下面是这些小朋友的计算方法。

```

>>> ret = 0                  #. 保存结果的变量
>>> item = 1                 # 每项的值
>>> while item <= 100:       # 求每项值的和
... ret = ret + item         # 求和
... item = item + 1          # 下一项
... # 结束 while 循环
>>> ret                      # 显示结果
5050                         # 结果
```

如果使用 range() 函数，代码可以简化成下面的样子。

```

>>> ret = 0                    # 保存结果的变量
>>> for item in range(1, 101): # 对于每一项求和
... ret = ret + item           # 求和
... # 结束 for 循环
>>> ret
5050
```

高斯发现这个序列是一个等差序列，就是相邻序列元素之间的差是相同的。基于该特性，高斯使用下面的求和公式：

所有元素的和=平均值×元素个数
平均值=（第一个元素+最后一个元素）/2

下面的代码实现了高斯的计算公式。

```

>>> ( 100 * (1 + 100 ) ) / 2 # 元素个数为 100， 第一个元素
5050.0
```

#### 7、Python map（处理函数，集合对象）：对所有元素依次进行处理

map() 函数接收两个参数：第一个参数是处理函数；第二个参数是一个集合对象。如果第二个参数类型不是一个集合，那么该函数抛出 TypeError 异常。

该函数对集合对象中的所有元素都调用第一个参数传入的处理函数，得到的是一个新的集合。原来的输入数据不会发生变化。

```

>>> def plus1(a):        # 定义一个处理函数
...     return a+1
...
>>> d = [1, 2, 3, 4]      # 操作数
>>> def plus1(a):         # 定义一个处理函数
...     return a+1
...
>>> d = [1, 2, 3, 4]      # 操作数
>>> r = map(plus1, d)     # 对操作数 d 的所有元素调用 plus1()
>>> r                     # 将结果放入列表 r 中
[2, 3, 4, 5]              # 查看 r 的内容
>>> d                     # 原来的输入 d 没有发生变化
[1, 2, 3, 4]
```

如果操作数是字典，则对字典的键进行处理。

```

>>> def plus1(a):        # 定义一个处理函数
...     return a+1
...
>>> d = {1: 100, 2: 200, 3:300}    # 操作数是一个字典
>>> r = map(plus1, d)              # 相当于是对 d.keys()进行操作
>>> r
[2, 3, 4]
```

如果操作数是集合，则对集合中每个元素按照集合内部的顺序依次进行处理，并返回一个列表。

```

>>> def plus1(a):        # 定义一个处理函数
...     return a+1
...
>>> a = set([1, 4, 6, 9])   # 定义一个字典
>>> a                       # 注意字典元素的顺序
set([1, 9, 4, 6])      
>>> r = map(plus1, a)
>>> r
[2, 10, 5, 7]
```

#### 8、reduce（处理函数，输入数据）：对所有元素依次进行合并处理

假定输入的数据是一个列表 [1,2,3,4,5]，处理函数是求和。进行以下操作，依次从输入数据中读出一个值，然后将其和结果值作为参数交给处理函数进行处理，并将元素结果保存到结果值中。结果值默认初始化为 0。如 reduce(f,data) 就等效于下面的 Python 代码。

```

ret = 0                     # 结果值初始化为 0
for x in data:              # 每次读入一个元素
    ret = f(ret, x)         # 将该元素和结果值进行处理，并将处理结果放入结果值中
return ret                  # 返回最后的结果值
```

下面实现一个列表求和操作，普通的方法如下：

```

>>> def add_op(x, y):  # 定义两个元素的求和操作
...     return x+y
...
>>> def get_sum(x):    # 定义多个元素的求和操作
...     ret = 0
...     for item in x:
...         ret = add_op(ret, item)
...     return ret
...
>>> d = [1, 2, 4, 5]   # 操作数
>>> get_sum(d)         # 求和
12
```

也可以使用 reduce() 快速实现列表求和操作，代码如下：

```

1     >>> def add_op(x, y):
...     return x+y
...
>>> def get_sum(x):            # 重新定义
...     return reduce(add_op, x)
>>> d = [1, 2, 4, 5]
>>> get_sum(d)
12
```

## 属性操作

在 Python 中，一切都是对象。对象有各种属性，如列表对象有长度属性。对于系统自带的数据类型，这些属性多数是只读的，我们的代码不能修改其值。但是对于自定义的类实例，则可以修改一些属性的值。通过类来定义自己的数据类型会在后续章节中介绍。

输出操作主要包括下面三个：

*   判断是否有某个属性——hasattr（对象，属性名）
*   设置某个属性——setattr（对象，属性名）
*   得到某个属性的值——getattr（对象，属性名）

```

>>> a = 12
>>> hasattr(a, 'bit_length')                   # 存在 bit_length 这个属性
True
>>> ret = getattr(a, 'bit_length')             #. 得到属性 bit_length 的值
>>> ret                                                                        # bit_length 是一个函数
<built-in method bit_length of int object at 0x1010e6700>
>>> ret()                                                              # 调用该函数
4                    
```