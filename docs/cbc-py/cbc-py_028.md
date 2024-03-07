# Python list()和 tuple()函数用法

前面己经提到，列表与元组最大的区别在于：元组是不可改变的，列表是可改变的。元组支持的操作，列表基本上都支持；列表支持对元素的修改，而元组则不支持。从这个角度来看，可以认为列表是增强版的元组。

虽然大部分时候都可使用列表来代替元组，但如果程序不需要修改列表所包含的元素，那么使用元组代替列表会更安全。

#### list() 函数

除使用前面介绍的方括号语法创建列表之外，Python 还提供了一个内置的 list() 函数来创建列表，list() 函数可用于将元组、区间（range）等对象转换为列表。

例如如下代码：

```
a_tuple = ('crazyit', 20, -1.2)
# 将元组转换成列表
a_list = list(a_tuple)
print(a_list)
# 使用 range()函数创建区间（range）对象
a_range = range(1, 5)
print(a_range) # range(1, 5)
# 将区间转换成列表
b_list = list(a_range)
print(b_list) #[1, 2, 3, 4]
# 创建区间时还指定步长
c_list = list(range(4, 20, 3))
print(c_list) # [4, 7, 10, 13, 16, 19]
```

上面程序中第 3 行代码使用 list() 将一个元组转换成列表；第 9 行代码用 list() 将一个区间（range）对象转换为列表，该行代码转换的区间为 range(1,5)，该区间使用默认步长 1，该区间共包括 1、2、3、4 四个值（注意不包括 5）。因此程序输出 b_list 时将看到如下结果：

[1, 2, 3, 4]

第 12 行代码将 range(4, 20, 3) 转换为列表，该区间是从 4 到 20（不包括 20）且步长为 3 的数值，因此程序输出 c_list 时将看到如下结果：

[4, 7, 10, 13, 16, 19]

Python 2.x 提供了一个 xrange() 函数，该函数与 Python 3.x 中的 range() 函数基本相同。Python 2.x 也提供了 range() 函数，但是该函数返回的是列表对象。

#### tuple() 函数

与 list() 对应的是，Python 也提供了一个 tuple() 函数，该函数可用于将列表、区间（range）等对象转换为元组。

例如如下代码：

```
a_list = ['crazyit', 20, -1.2]
# 将列表转换成元组
a_tuple = tuple(a_list)
print(a_tuple)
# 使用 range()函数创建区间（range）对象
a_range = range(1, 5)
print(a_range) # range(1, 5)
# 将区间转换成元组
b_tuple = tuple(a_range)
print(b_tuple) #[1, 2, 3, 4]
# 创建区间时还指定步长
c_tuple = tuple(range(4, 20, 3))
print(c_tuple) # [4, 7, 10, 13, 16, 19]
```

上面代码中的第 3、9、12 行代码正是使用 tuple() 函数创建元组的关键代码。