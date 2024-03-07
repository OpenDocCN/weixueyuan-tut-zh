# Python Counter 类用法完全攻略

collections 包下的 Counter 也是一个很有用的工具类，它可以自动统计容器中各元素出现的次数。

Counter 的本质就是一个特殊的 dict，只不过它的 key 都是其所包含的元素，而它的 value 则记录了该 key 出现的次数。因此，如果通过 Counter 并不存在的 key 访问 value，将会输出 0（代表该 key 出现了 0 次）。

程序可通过任何可法代对象参数来创建 Counter 对象，此时 Counter 将会自动统计各元素出现的次数，并以元素为 key，出现的次数为 value 来构建 Counter 对象；程序也能以 dict 为参数来构建 Counter 对象；还能通过关键字参数来构建 Counter 对象。例如如下程序：

```
from collections import Counter
# 创建空的 Counter 对象
c1 = Counter()
# 以可迭代对象创建 Counter 对象
c2 = Counter('hannah')
print(c2)
# 以可迭代对象创建 Counter 对象
c3 = Counter(['Python', 'Swift', 'Swift', 'Python', 'Kotlin', 'Python'])
print(c3)
# 以 dict 来创建 Counter 对象
c4 = Counter({'red': 4, 'blue': 2})
print(c4)
# 使用关键字参数的语法创建 Counter
c5 = Counter(Python=4, Swift=8)
print(c5)
```

运行上面程序，可以看到如下输出结果：

Counter({'h': 2, 'a': 2, 'n': 2})
Counter({'Python': 3, 'Swift': 2, 'Kotlin': 1})
Counter({'red': 4, 'blue': 2})
Counter({'Swift': 8, 'Python': 4})

事实上，Counter 继承了 dict 类，因此它完全可以调用 dict 所支持的方法。此外，Counter 还提供了如下三个常用的方法：

*   elements()：该方法返回该 Counter 所包含的全部元素组成的迭代器。
*   most_common([n])：该方法返回 Counter 中出现最多的 n 个元素。
*   subtract([iterable-or-mapping])：该方法计算 Counter 的减法，其实就是计算减去之后各元素出现的次数。

下面程序示范了 Counter 类中这些方法的用法示例：

```
from collections import Counter
# 创建 Counter 对象
cnt = Counter()
# 访问并不存在的 key，将输出该 key 的次数为 0.
print(cnt['Python']) # 0
for word in ['Swift', 'Python', 'Kotlin', 'Kotlin', 'Swift', 'Go']:
    cnt[word] += 1
print(cnt)
# 只访问 Counter 对象的元素
print(list(cnt.elements()))
# 将字符串（迭代器）转换成 Counter
chr_cnt = Counter('abracadabra')
# 获取出现最多的 3 个字母
print(chr_cnt.most_common(3))  # [('a', 5), ('b', 2), ('r', 2)]
c = Counter(a=4, b=2, c=0, d=-2)
d = Counter(a=1, b=2, c=3, d=4)
# 用 Counter 对象执行减法，其实就是减少各元素的出现次数
c.subtract(d)
print(c) # Counter({'a': 3, 'b': 0, 'c': -3, 'd': -6})
e = Counter({'x': 2, 'y': 3, 'z': -4})
# 调用 del 删除 key-value 对，会真正删除该 key-value 对
del e['y']
print(e)
# 访问'w'对应的 value，'w'没有出现过，因此返回 0
print(e['w']) # 0
# 删除 e['w']，删除该 key-value 对
del e['w']
# 再次访问'w'对应的 value，'w'还是没有，因此返回 0
print(e['w']) # 0
```

上面程序中第 10 行代码调用了 Counter 对象的 elements() 方法，该方法返回容器中所有元素组成的迭代器，Counter 记录了几个元素的出现次数，该方法就会返回几个对应的元素。

程序中第 14 行代码调用了 Counter 对象的 most_common(3) 方法，该方法会返回容器中出现次数最多的三个元素。

程序中第 18 行代码调用了 Counter 对象的 subtract() 方法执行减法，实质上就是对元素出现的次数执行减法。

运行上面代码，可以看到如下输出结果：

0
Counter({'Swift': 2, 'Kotlin': 2, 'Python': 1, 'Go': 1})
['Swift', 'Swift', 'Python', 'Kotlin', 'Kotlin', 'Go']
[('a', 5), ('b', 2), ('r', 2)]
Counter({'a': 3, 'b': 0, 'c': -3, 'd': -6})
Counter({'x': 2, 'z': -4})
0
0

此外，对于 Counter 对象还有一些很常用的操作，比如把 Counter 对象转换成 set（集合）、list（列表）、dict（字典）等，程序还可对 Counter 执行加、减、交、并运算，对 Counter 进行求正、求负运算等。对 Counter 执行各种运算的含义如下：

*   加：将两个 Counter 对象中各 key 出现的次数相加，且只保留出现次数为正的元素。
*   减：将两个 Counter 对象中各 key 出现的次数相喊，且只保留出现次数为正的元素。
*   交：取两个 Counter 对象中都出现的 key 且各 key 对应的次数的最小数。
*   并：取两个 Counter 对象中各 key 对应的出现次数的最大数。
*   求正：只保留 Counter 对象中出现次数为 0 或正数的 key-value 对。
*   求负：只保留 Counter 对象中出现次数为负数的 key-value 对，并将出现次数改为正数。

下面程序示范了对 Counter 对象进行的这些常用操作：

```
from collections import Counter
# 创建 Counter 对象
c = Counter(Python=4, Swift=2, Kotlin=3, Go=-2)
# 统计 Counter 中所有出现次数的总和
print(sum(c.values())) # 7
# 将 Counter 转换为 list，只保留各 key
print(list(c)) # ['Python', 'Swift', 'Kotlin', 'Go']
# 将 Counter 转换为 set，只保留各 key
print(set(c)) # {'Go', 'Python', 'Swift', 'Kotlin'}
# 将 Counter 转换为 dict
print(dict(c)) # {'Python': 4, 'Swift': 2, 'Kotlin': 3, 'Go': -2}
# 将 Counter 转换为 list，列表元素都是(元素, 出现次数)组
list_of_pairs = c.items()
print(list_of_pairs) # dict_items([('Python', 4), ('Swift', 2), ('Kotlin', 3), ('Go', -2)])
# 将列表元素为(元素, 出现次数)组的 list 转换成 Counter
c2 = Counter(dict(list_of_pairs))
print(c2) # Counter({'Python': 4, 'Kotlin': 3, 'Swift': 2, 'Go': -2})
# 获取 Counter 中最少出现的 3 个元素
print(c.most_common()[:-4:-1]) # [('Go', -2), ('Swift', 2), ('Kotlin', 3)]
# 清空所有 key-value 对
c.clear()
print(c) # Counter()
c = Counter(a=3, b=1, c=-1)
d = Counter(a=1, b=-2, d=3)
# 对 Counter 执行加法
print(c + d)  # Counter({'a': 4, 'd': 3})
# 对 Counter 执行减法
print(c - d)  # Counter({'b': 3, 'a': 2})
Counter({'a': 2})
# 对 Counter 执行交运算
print(c & d) # Counter({'a': 1})
print(c | d) # Counter({'a': 3, 'd': 3, 'b': 1})
print(+c) # Counter({'a': 3, 'b': 1})
print(-d) # Counter({'b': 2})
```

上面程序演示了前面介绍的 Counter 的各种通用方法和运算符，并且还给出了每次运算的输出结果，读者可通过这些输出结果来理解 Counter 对象的方法和运算符的功能。