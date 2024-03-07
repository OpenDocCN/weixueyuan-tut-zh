# Python True 和 False（布尔类型）

> 原文：[`www.weixueyuan.net/a/380.html`](http://www.weixueyuan.net/a/380.html)

布尔类型仅用来表示两种可能的值：真（True）和假（False）。在 Python 中，布尔类型是一种特殊的整数类型。

下面的例子可以看到 True 和 False 既是布尔类型，也是整数类型。

```

>>> isinstance(True, bool)   # True 是布尔类型？
True
>>> isinstance(True, int)    # True 是整数类型？
True
>>> isinstance(False, bool)  # False 是布尔类型？
True
>>> isinstance(False, int)   # False 是 int 类型？
True
```

其实，True 就是整数 1，False 就是整数 0。

```

>>> True == 1    # True 和 1 的值相等吗？
True
>>> True == 2    # True 和 2 的值相等吗？
False
>>> False == 1   # False 和 1 的值相等吗？
False
>>> False == 0   # False 和 0 的值相等吗？
True
```

由于布尔类型就是整数类型的一个特例，所以整数类型的所有运算对布尔类型都适用。如前面介绍的移位运算也是可以的：

```

>>> True << 3   # 对布尔类型数据进行移位操作
8
>>> False + 12  # 对布尔类型数据进行加法运算
12
```

Python 对布尔类型有一些特殊的运算，也就是与或非，请猛击《Python 逻辑运算》一文了解详情。