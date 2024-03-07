# Python tuple 元组的用法

> 原文：[`www.weixueyuan.net/a/428.html`](http://www.weixueyuan.net/a/428.html)

Python 元组和列表很像，也是一个包含多个元素的集合。与列表不同的是不能对元组中数据进行修改，如不能为元组添加数据，也不能删除元组中的某个元素。元组最常用的用法是作为函数的返回值。

## Python 元组的表示法

元组的最简单表示法是“（元素 1，元素 2，…）”。这个和列表的表示方法类似，只是将方括号变成圆括号。下面是这种最简单的表示法的例子。

```

>>> tuple1 = (1, 2, 3)
>>> type(tuple1)
<type 'tuple'>
```

但是对于空的元组，需要使用下面的表示法：

```

>>> tuple1 = tuple()
>>> type(tuple1)
<type 'tuple'>
>>> len(tuple1)
0
```

对于仅包含一个元素的元组，需要使用下面的方法：

```

>>> tuple1 = tuple([1])
>>> type(tuple1)
<type 'tuple'>
>>> len(tuple1)
1
```

或者使用下面的方法：

```

>>> tuple1 = (1,)
>>> type(tuple1)
<type 'tuple'>
>>> len(tuple1)
1
```

但是不能使用下面的方式：

```

>>> tuple1 = (1)  # (1)等效于 1
>>> type(tuple1)  # 可以看到类型是整型 int 而不是元组 tuple
<type 'int'>
```

## Python 元组接口函数

元组的接口函数比列表少很多，常用的只有两个：

*   一个是 count()，表示某个值在元组中出现的次数；
*   另外一个是 index()，表示某个值在元组中第一次出现的位置。

这两个接口函数的用法和列表是一样的。

#### 1) 某个元素值出现的次数——count（值）

如果没有出现过，返回 0，否则返回一个正整数表示该值在元组中出现的次数。

```

>>> a = (1, 3, 5, 7)  # 定义了元组 a，包含 4 个元素
>>> type(a)           # 查看 a 的类型，这是 Python 3 中的返回值
<class 'tuple'>
>>> a.count(1)        # 1 在 a 中出现了 1 次
1
>>> a.count(2)        # 2 在 a 中出现了 0 次
0
```

#### 2) 指定值在元组中第一次出现的位置——index（值）

如果指定的值在元组中不存在，抛出异常 ValueError，否则返回该值在元组中第一次出现的位置。注意，元组中第一个元素的位置是 0，第二个元素的位置是 1，以此类推。

```

>>> a = (100, 90, 80, 90, 80, 70)  # 定义元组 a
>>> len(a)  # a 包含 6 个元素
6
>>> a.count(90)  # 其包含 2 个 90
2
>>> a.index(90)  # 第一个 90 出现的位置是 1，就是第二个元素
1
>>> a.index(10)  # 10 没有被 a 包含，所以 index()会抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: tuple.index(x): x not in tuple
```