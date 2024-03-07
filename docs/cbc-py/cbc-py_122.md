# Python OrderedDict 用法详解

OrderedDict 也是 dict 的子类，其最大特征是，它可以“维护”添加 key-value 对的顺序。简单来说，就是先添加的 key-value 对排在前面，后添加的 key-value 对排在后面。

由于 OrderedDict 能维护 key-value 对的添加顺序，因此即使两个 OrderedDict 中的 key-value 对完全相同，但只要它们的顺序不同，程序在判断它们是否相等时也依然会返回 false。

例如如下程序：

```
from collections import OrderedDict
# 创建 OrderedDict 对象
dx = OrderedDict(b=5, c=2, a=7)
print(dx) # OrderedDict([('b', 5), ('c', 2), ('a', 7)])
d = OrderedDict()
# 向 OrderedDict 中添加 key-value 对
d['Python'] = 89
d['Swift'] = 92
d['Kotlin'] = 97
d['Go'] = 87
# 遍历 OrderedDict 的 key-value 对
for k,v in d.items():
    print(k, v)
```

上面程序首先创建了 OrderedDict 对象，接下来程序向其中添加了 4 个 key-value 对，OrderedDict 完全可以“记住”它们的添加顺序。运行该程序，可以看到如下输出结果：

OrderedDict([('b', 5), ('c', 2), ('a', 7)])
Python 89
Swift 92
Kotlin 97
Go 87

正如前面所说的，两个 OrderedDict 中即使包含的 key-value 对完全相同，但只要它们的顺序不同，程序也依然会判断出两个 OrderedDict 是不相等的。例如如下程序：

```
# 创建普通的 dict 对象
my_data = {'Python': 20, 'Swift':32, 'Kotlin': 43, 'Go': 25}
# 创建基于 key 排序的 OrderedDict
d1 = OrderedDict(sorted(my_data.items(), key=lambda t: t[0]))
# 创建基于 value 排序的 OrderedDict
d2 = OrderedDict(sorted(my_data.items(), key=lambda t: t[1]))
print(d1) # OrderedDict([('Go', 25), ('Kotlin', 43), ('Python', 20), ('Swift', 32)])
print(d2) # OrderedDict([('Python', 20), ('Go', 25), ('Swift', 32), ('Kotlin', 43)])
print(d1 == d2) # False
```

上面程序先创建了一个普通的 dict 对象，该对象中包含 4 个 key-value 对；接下来程序分别使用 sorted() 函数对 my_data（dict 对象）的 items 进行排序：d1 是按 key 排序的；d2 是按 value 排序的，这样得到的 d1、d2 两个 OrderedDict 中的 key-value 对是一样的，只不过顺序不同。

运行上面程序，可以看到如下输出结果：

OrderedDict([('Go', 25), ('Kotlin', 43), ('Python', 20), ('Swift', 32)])
OrderedDict([('Python', 20), ('Go', 25), ('Swift', 32), ('Kotlin', 43)])
False

从上面的输出结果可以看到，虽然两个 OrderedDict 所包含的 key-value 对完全相同，但由于它们的顺序不同，因此程序判断它们不相等。

此外，由于 OrderedDict 是有序的，因此 Python 为之提供了如下两个方法：

1.  popitem(last=True)：默认弹出并返回最右边（最后加入）的 key-value 对；如果将 last 参数设为 False，则弹出并返回最左边（最先加入）的 key-value 对。
2.  move_to_end(key, last=True)：默认将指定的 key-value 对移动到最右边（最后加入）；如果将 last 改为 False，则将指定的 key-value 对移动到最左边（最先加入）。

下面程序示范了 OrderedDict 的两个方法的用法：

```
from collections import OrderedDict
d = OrderedDict.fromkeys('abcde')
# 将 b 对应的 key-value 对移动到最右边（最后加入）
d.move_to_end('b')
print(d.keys()) # odict_keys(['a', 'c', 'd', 'e', 'b'])
# 将 b 对应的 key-value 对移动到最左边（最先加入）
d.move_to_end('b', last=False)
print(d.keys()) # odict_keys(['b', 'a', 'c', 'd', 'e'])
# 弹出并返回最右边（最后加入）的 key-value 对
print(d.popitem()[0]) # e
# 弹出并返回最左边（最先加入）的 key-value 对
print(d.popitem(last=False)[0]) # b
```

运行上面程序，可以看到如下输出结果：

odict_keys(['a', 'c', 'd', 'e', 'b'])
odict_keys(['b', 'a', 'c', 'd', 'e'])
e
b

通过上面的输出结果可以看出，使用 OrderedDict 的 move_to_end() 方法可以方便地将指定的 key-value 对移动到 OrderedDict 的任意一端；而 popitem() 方法则可用于弹出并返回 OrderedDict 任意一端的 key-value 对。