# Python defaultdict 用法（带实例分析）

defaultdict 是 dict 的子类，因此 defaultdict 也可被当成 dict 来使用，dict 支持的功能，defaultdict 基本都支持。但它与 dict 最大的区别在于，如果程序试图根据不存在的 key 采访问 dict 中对应的 value，则会引发 KeyError 异常；而 defaultdict 则可以提供一个 default_factory 属性，该属性所指定的函数负责为不存在的 key 来生成 value。

通过下面程序进行对比：

```
from collections import defaultdict
my_dict = {}
# 使用 int 作为 defaultdict 的 default_factory
# 将 key 不存在时，将会返回 int()函数的返回值
my_defaultdict = defaultdict(int)
print(my_defaultdict['a']) # 0
print(my_dict['a']) # KeyError
```

上面程序分别创建了空的 dict 对象和 defaultdict 对象，当程序试图访问 defaultdict 中不存在的 key 对应的 value 时，程序输出 defaultdict 的 default_factory 属性（int 函数）的返回值 0；如果程序试图访问 dict 中不存在的 key 对应的 value，就会引发 KeyError 异常。

假如程序中包含多个 key-value 对数据，在这些 key-value 对中有些 key 是重复的，程序希望对这些 key-value 对进行整理，key 对应一个 list，该 list 中包含这组数据中该 key 对应的所有 value。

下面先使用普通 dict 来完成这项工作：

```
s = [('Python', 1), ('Swift', 2), ('Python', 3), ('Swift', 4), ('Python', 9)]
d = {}
for k, v in s:
    # setdefault()方法用于获取指定 key 对应的 value.
    # 如果该 key 不存在，则先将该 key 对应的 value 设置为默认值:[]
    d.setdefault(k, []).append(v)
print(list(d.items()))
```

正如从上面第 6 行代码所看到的，如果使用普通 dict 来处理，就需要处理 key 不存在的情况。程序中使用了 dict 的 setdefault() 方法，该方法用于获取指定 key 对应的 value，但如果该 key 不存在，setdefault() 方法就会先为该 key 设置一个默认的 value。

运行上面程序，可以看到如下输出结果：

[('Python', [1, 3, 9]), ('Swift', [2, 4])]

如果使用 defaultdict 来处理则简单得多，因为程序可以直接为 defaultdict 中不存在的 key 设置默认的 value。该处理程序如下：

```
from collections import defaultdict
s = [('Python', 1), ('Swift', 2), ('Python', 3), ('Swift', 4), ('Python', 9)]
# 创建 defaultdict，设置由 list()函数来生成默认值
d = defaultdict(list)
for k, v in s:
    # 直接访问 defaultdict 中指定 key 对应的 value 即可。
    # 如果该 key 不存在，defaultdict 会自动为该 key 生成默认值
    d[k].append(v)
print(list(d.items()))
```

对比该程序中的第 8 行代码和前一个程序中的第 6 行代码，不难发现使用 defaultdict 更加方便，原因是程序直接访问 defaultdict 中指定的 key 对应的 value，如果该 key 不存在，程序在创建 defaultdict 时传入的 list 函数将会为之生成默认的 value。