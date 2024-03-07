# Python list 常用方法（count、index、pop、reverse 和 sort）快速攻略

除前面章节介绍的增加元素、删除元素、修改元素方法之外，列表还包含了一些常用的方法。

例如，在交互式解释器中输入 dir(list) 即可看到列表包含的所有方法，如下所示：

>>> dir(list)
['append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
>>>

在上面输出结果中己经剔除了那些以双下画线开头的方法。按照约定，这些方法都具有特殊的意义，不希望被用户直接调用。

上面有些方法前面己经介绍过了，列表还包含如下常用方法可以使用：

*   count()：用于统计列表中某个元素出现的次数。
*   index()：用于判断某个元素在列表中出现的位置。
*   pop()：用于将列表当成“栈”使用，实现元素出栈功能。
*   reverse()：用于将列表中的元素反向存放。
*   sort()：用于对列表元素排序。

## count()用法

下面代码示范了 count() 方法的用法：

```
a_list = [2, 30, 'a', [5, 30], 30]
# 计算列表中 30 的出现次数
print(a_list.count(30)) # 2
# 计算列表中[5, 30]的出现次数
print(a_list.count([5, 30])) # 1
```

## index()用法

index() 方法则用于定位某个元素在列表中出现的位置，如果该元素没有出现，则会引发 ValueError 错误。在使用 index() 方法时还可传入 start、end 参数，用于在列表的指定范围内搜索元素。

如下代码示范了 index() 方法的用法：

```
a_list = [2, 30, 'a', 'b', 'crazyit', 30]
# 定位元素 30 的出现位置
print(a_list.index(30)) # 1
# 从索引 2 处开始、定位元素 30 的出现位置
print(a_list.index(30, 2)) # 5
# 从索引 2 处到索引 4 处之间定位元素 30 的出现位置，找不到该元素
print(a_list.index(30, 2, 4)) # ValueError
```

## pop()用法

pop() 方法用于实现元素出栈功能。栈是一种特殊的数据结构，它可实现先入后出（FILO）功能，即先加入栈的元素，反而后出栈。

在其他编程语言所实现的“栈”中，往往会提供一个 push() 方法，用于实现入栈操作，但 Python 的列表并没有提供 push() 方法，我们可以使用 append() 方法来代替 push() 方法实现入栈操作。

下面代码示范了使用列表作为“栈”的示例：

```
stack = []
# 向栈中“入栈”3 个元素
stack.append("fkit")
stack.append("crazyit")
stack.append("Charlie")
print(stack) # ['fkit', 'crazyit', 'Charlie']
# 第一次出栈：最后入栈的元素被移出栈
print(stack.pop())
print(stack) # ['fkit', 'crazyit']
# 再次出栈
print(stack.pop())
print(stack) # ['fkit']
```

上面程序中，第 8 行实现了第一次出栈操作，该操作将会把最后一次添加的元素移出栈，且该方法会返回出栈的元素。因此，执行这行代码将会看到输出 Charlie。

与所有编程语言类似的是，出栈操作既会移出列表的最后一个元素，也会返回被移出的元素。

## reverse()用法

reverse() 方法会将列表中所有元素的顺序反转。例如如下代码：

```
a_list = list(range(1, 8))
# 将 a_list 列表元素反转
a_list.reverse()
print(a_list) # [7, 6, 5, 4, 3, 2, 1]
```

从上面的运行结果可以看出，调用 reverse() 方法将反转列表中的所有元素。

## sort()用法

sort() 方法用于对列表元素进行排序。例如如下代码：

```
a_list = [3, 4, -2, -30, 14, 9.3, 3.4]
# 对列表元素排序
a_list.sort()
print(a_list) #[-30, -2, 3, 3.4, 4, 9.3, 14]
b_list = ['Python', 'Swift', 'Ruby', 'Go', 'Kotlin', 'Erlang']
# 对列表元素排序：默认按字符串包含的字符的编码大小比较
b_list.sort()
print(b_list) # ['Erlang', 'Go', 'Kotlin', 'Python', 'Ruby', 'Swift']
```

sort() 方法除支持默认排序之外，还可传入 key 和 reverse 两个参数，而且这两个参数必须通过参数名指定（这种参数叫关键字参数，后续章节会详细介绍）：

*   key 参数用于为每个元素都生成一个比较大小的“键”；
*   reverse 参数则用于执行是否需要反转排序，默认是从小到大排序；如果将该参数设为 True，将会改为从大到小排序。

如下代码示范了 key 和 reverse 参数的用法：

```
# 指定 key 为 len，指定使用 len 函数对集合元素生成比较的键，
# 也就是按字符串的长度比较大小
b_list.sort(key=len)
print(b_list) # ['Go', 'Ruby', 'Swift', 'Erlang', 'Kotlin', 'Python']
# 指定反向排序
b_list.sort(key=len, reverse=True)
print(b_list) # ['Erlang', 'Kotlin', 'Python', 'Swift', 'Ruby', 'Go']
```

上面两次排序时都将 key 参数指定为 len，这意味着程序将会使用 len() 函数对集合元素生成比较大小的键，即根据集合元素的字符串长度比较大小。

需要指出的是，在 Python 2.x 中，列表的 sort() 方法还可传入一个比较大小的函数，该函数负责比较列表元素的大小。该函数包含两个参数，当该函数返回正整数时，代表该函数的第一个参数大于第二个参数；当该函数返回负整数时，代表该函数的第一个参数小于第二个参数；返回 0，则意味着两个参数相等。

下面代码示范了使用比较函数调用 sort() 方法。以下代码只能在 Python 2.x 中执行：

```
# 以下代码只能在 Python 2.x 中执行
# 定义一个根据长度比较大小的比较函数
def len_cmp(x, y):
    # 下面代码比较大小的逻辑是：长度大的字符串就算更大
    return 1 if len(x) > len(y) else (-1 if len(x) < len(y) else 0)
b_list.sort(len_cmp)
print(b_list) #['Go', 'Ruby', 'Swift', 'Erlang', 'Kotlin', 'Python']
```

执行上面代码，同样可以看到列表元素按长度大小从小到大排序。