# Python list 列表删除元素（3 种方法）

删除列表元素使用 del 语句。del 语句是 Python 的一种语句，专门用于执行删除操作，不仅可用于删除列表的元素，也可用于删除变量等。

使用 del 语句既可删除列表中的单个元素，也可直接删除列表的中间一段。例如如下代码：

```
a_list = ['crazyit', 20, -2.4, (3, 4), 'fkit']
# 删除第 3 个元素
del a_list[2]
print(a_list) # ['crazyit', 20, (3, 4), 'fkit']
# 删除第 2 个到第 4 个（不包含）元素
del a_list[1: 3]
print(a_list) # ['crazyit', 'fkit']
b_list = list(range(1, 10))
# 删除第 3 个到倒数第 2 个（不包含）元素，间隔为 2
del b_list[2: -2: 2]
print(b_list) # [1, 2, 4, 6, 8, 9]
# 删除第 3 个到第 5 个（不包含）元素
del b_list[2: 4]
print(b_list) # [1, 2, 8, 9]
```

上面程序中，第 3 行代码简单地删除了列表中的一个元素；第 6 行代码执行删除时，使用了列表的 slice 语法，因此该删除操作将会删除列表的中间一段；第 10 行代码执行删除时，同样使用了列表的 slice 语法，并指定了间隔，因此将会看到以间隔为 2 的方式删除列表的中间一段。

使用 del 语句不仅可以删除列表元素，也可以删除普通变量，例如如下代码：

```
name = 'crazyit'
print(name) # crazyit
# 删除 name 变量
del name
#print(name) # NameError
```

程序中第 4 行代码删除了 name 变量，因此接下来访问 name 变量时将会引发 NameError 错误。

除使用 del 语句之外，Python 还提供了 remove() 方法来删除列表元素，该方法并不是根据索引来删除元素的，而是根据元素本身来执行删除操作的。该方法只删除第一个找到的元素，如果找不到该元素，该方法将会引发 ValueError 错误。

如下代码示范了使用 remove() 方法删除元素：

```
c_list = [20, 'crazyit', 30, -4, 'crazyit', 3.4]
# 删除第一次找到的 30
c_list.remove(30)
print(c_list) # [20, 'crazyit', -4, 'crazyit', 3.4]
# 删除第一次找到的'crazyit'
c_list.remove('crazyit')
print(c_list) # [20, -4, 'crazyit', 3.4]
```

列表还包含一个 clear() 方法，正如它的名字所暗示的，该方法用于清空列表的所有元素。例如如下代码：

```
c_list.clear()
print(c_list) # []
```