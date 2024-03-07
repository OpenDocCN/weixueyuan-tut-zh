# Python set 和 frozenset 集合操作（附带两者区别）

set 集合是可变容器，程序可以改变容器中的元素。与 set 对应的还有 frozenset 集合，frozenset 是 set 的不可变版本，它的元素是不可变的。

## Python set 集合

set 集合有如下两个特征：

*   set 不记录元素的添加顺序。
*   元素不允许重复。

在交互式解释器中输入 `[e for e in dir(set) if not e.startswith('_')]` 来查看 set 集合的全部方法，可以看到如下输出结果：

>>> [e for e in dir(set) if not e.startswith('_')]
['add', 'clear', 'copy', 'difference', 'difference_update', 'discard', 'intersection', 'intersection_update', 'isdisjoint', 'issubset', 'issuperset', 'pop', 'remove', 'symmetric_difference', 'symmetric_difference_update', 'union', 'update']

对于上面这些方法，其方法名己经暗示了它们的作用，比如 add() 很明显就是向 set 集合中添加元素，remove()、discard() 就是删除元素，clear() 就是清空白 set 集合，等等。

remove() 与 discard() 方法都用于删除集合中的元素，但区别在于，如果集合中不包含被删除的元素，remove() 方法会报出 KeyError 异常，而 discard() 方法则什么也不做。

下面程序示范了 set 集合的方法的用法：

```
# 使用花括号构建 set 集合
c = {'白骨精'}
# 添加元素
c.add("孙悟空")
c.add(6)
print("c 集合的元素个数为:" , len(c)) # 输出 3
# 删除指定元素
c.remove(6)
print("c 集合的元素个数为:" , len(c)) # 输出 2
# 判断是否包含指定字符串
print("c 集合是否包含'孙悟空'字符串:" , ("孙悟空" in c)) # 输出 True
c.add("轻量级 Java EE 企业应用实战")
print("c 集合的元素：" , c)
# 使用 set()函数（构造器）来创建 set 集合
books = set()
books.add("轻量级 Java EE 企业应用实战")
books.add("疯狂 Java 讲义")
print("books 集合的元素：" , books)
# issubset()方法判断是否为子集合
print("books 集合是否为 c 的子集合？", books.issubset(c)) # 输出 False
# issubset()方法与<=运算符效果相同
print("books 集合是否为 c 的子集合？", (books <= c)) # 输出 False
# issuperset()方法判断是否为父集合
# issubset 和 issuperset 其实就是倒过来判断
print("c 集合是否完全包含 books 集合？", c.issuperset(books)) # 输出 False
# issuperset()方法与>=运算符效果相同
print("c 集合是否完全包含 books 集合？", (c >= books)) # 输出 False
# 用 c 集合减去 books 集合里的元素，不改变 c 集合本身
result1 = c - books
print(result1)
# difference()方法也是对集合做减法，与用-执行运算的效果完全一样
result2 = c.difference(books)
print(result2)
# 用 c 集合减去 books 集合里的元素，改变 c 集合本身
c.difference_update(books)
print("c 集合的元素：" , c)
# 删除 c 集合里的所有元素
c.clear()
print("c 集合的元素：" , c)
# 直接创建包含元素的集合
d = {"疯狂 Java 讲义", '疯狂 Python 讲义', '疯狂 Kotlin 讲义'}
print("d 集合的元素：" , d)
# 计算两个集合的交集，不改变 d 集合本身
inter1 = d & books
print(inter1)
# intersection()方法也是获取两个集合的交集，与用&执行运算的效果完全一样
inter2 = d.intersection(books)
print(inter2)
# 计算两个集合的交集，改变 d 集合本身
d.intersection_update(books)
print("d 集合的元素：" , d)
# 将 range 对象包装成 set 集合
e = set(range(5))
f = set(range(3, 7))
print("e 集合的元素：" , e)
print("f 集合的元素：" , f)
# 对两个集合执行异或运算
xor = e ^ f
print('e 和 f 执行 xor 的结果：', xor)
# 计算两个集合的并集，不改变 e 集合本身
un = e.union(f)
print('e 和 f 执行并集的结果：', un)
# 计算两个集合的并集，改变 e 集合本身
e.update(f)
print('e 集合的元素：', e)
```

上面程序基本示范了 set 集合中所有方法的用法。不仅如此，该程序还示范了 set 集合支持的如下几个运算符：

*   <=：相当于调用 issubset() 方法，判断前面的 set 集合是否为后面的 set 集合的子集合。
*   >=：相当于调用 issuperset() 方法，判断前面的 set 集合是否为后面的 set 集合的父集合。
*   -：相当于调用 difference() 方法，用前面的 set 集合减去后面的 set 集合的元素。
*   &：相当于调用 intersection() 方法，用于获取两个 set 集舍的交集。
*   ^：计算两个集合异或的结果，就是用两个集合的并集减去交集的元素。

此外，由于 set 集合本身是可变的，因此它除了提供 add()、remove()、discard() 方法来操作单个元素，还支持进行集合运算来改变集合内的元素。因此，它的集合运算方法都有两个版本：

1.  交集运算：intersection() 和 intersection_update()，前者不改变集合本身，而是返回两个集合的交集；后者会通过交集运算改变第一个集合。
2.  并集运算：union() 和 update()，前者不改变集合本身，而是返回两个集合的并集；后者会通过并集运算改变第一个集合。
3.  减法运算：difference() 和 difference_update()，前者不改变集合本身，而是返回两个集合做咸法的结果；后者改变第一个集合。

## Python frozenset 集合

frozenset 是 set 的不可变版本，因此 set 集合中所有能改变集合本身的方法（如 add、remove、discard、xxx_update 等），frozenset 都不支持；set 集合中不改变集合本身的方法，fronzenset 都支持。

在交互式解释器中输入 `[e for e in dir(frozenset) if not e.startswith('_')]` 命令来查看 frozenset 集合的全部方法，可以看到如下输出结果：

[e for e in dir(frozenset) if not e.startswith('_')]
['copy', 'difference', 'intersection', 'isdisjoint', 'issubset', 'issuperset', 'symmetric_difference', 'union']

很明显，frozenset 的这些方法和 set 集合的同名方法的功能完全相同。

frozenset 的作用主要有两点：

*   当集合元素不需要改变时，使用 frozenset 代替 set 更安全。
*   当某些 API 需要不可变对象时，必须用 frozenset 代替 set。比如 dict 的 key 必须是不可变对象，因此只能用 frozenset；再比如 set 本身的集合元素必须是不可变的，因此 set 不能包含 set，set 只能包含 frozenset。

如下程序示范了在 set 中添加 frozenset：

```
s = set()
frozen_s = frozenset('Kotlin')
# 为 set 集合添加 frozenset
s.add(frozen_s)
print('s 集合的元素：', s)
sub_s = {'Python'}
# 为 set 集合添加普通 set 集合，程序报错
s.add(sub_s)
```

上面程序中第 4 行代码为 set 添加 frozenset，程序完全没有问题，这样 set 中就包括一个 frozenset 子集合；第 8 行代码试图向 set 中添加普通 set，程序会报出 TypeError 异常。运行上面代码，可以看到如下输出结果：

s 集合的元素： {frozenset({'t', 'o', 'i', 'K', 'l', 'n'})}
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 8, in <module>
    s.add(sub_s)
TypeError: unhashable type: 'set'