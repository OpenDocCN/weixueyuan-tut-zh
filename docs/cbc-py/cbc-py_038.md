# Python while 循环详解

while 循环的语法格式如下：

[init_statements]
while test_expression :
    body_statements
    [iteration_statements]

while 循环在每次执行循环体之前，都要先对 test_expression 循环条件求值，如果循环条件为真，则运行循环体部分。从上面的语法格式来看，迭代语句 iteration_statements 总是位于循环体的最后，因此只有当循环体能成功执行完成时，while 循环才会执行迭代语句 iteration_statements。

从这个意义上看，while 循环也可被当成分支语句使用，即如果 test_expression 条件一开始就为假，则循环体部分将永远不会获得执行的机会。

下面程序示范了一个简单的 while 循环：

```
# 循环的初始化条件
count_i = 0
# 当 count_i 小于 10 时，执行循环体
while count_i < 10 :
    print("count:", count_i)
    # 迭代语句
    count_i += 1
print("循环结束!")
```

在使用 while 循环时，一定要保证循环条件有变成假的时候：否则这个循环将成为一个死循环，永远无法结束这个循环。例如如下代码：

```
# 下面是一个死循环
count_i2 = 0
while count_i2 < 10 :
    print("不停执行的死循环:", count_i2)
    count_i2 -=1
print("永远无法跳出的循环体")
```

在上面代码中，count_i2 的值越来越小，这将导致 count_i2 的值永远小于 10，count_i2<10 循环条件一直为 True，从而导致这个循环永远无法结束。

与前面介绍分支语句类似的是，while 循环的循环体中所有代码必须使用相同的缩进，否则 Python 也会引发错误。例如如下程序：

```
# 循环的初始化条件
count_i = 0
# 当 count 小于 10 时，执行循环体
while count_i < 10:
    print('count_i 的值', count_i)
count_i += 1
```

运行上面程序，将会看到执行一个死循环。这是由于 count_i += 1 代码没有缩进，这行代码就不属于循环体。这样程序中的 count_1 将一直是 0，从而导致 count_i < 10 一直都是 True，因此该循环就变成了一个死循环。

## 使用 while 循环遍历列表和元组

由于列表和元组的元素都是有索引的，因此程序可通过 while 循环、列表或元组的索引来遍历列表和元组中的所有元素。例如如下程序：

```
a_tuple = ('fkit', 'crazyit', 'Charli')
i = 0
# 只有 i 小于 len(a_list)，继续执行循环体
while i < len(a_tuple):
    print(a_tuple[i]) # 根据 i 来访问元组的元素
    i += 1

```

运行上面程序，可以看到如下输出结果：

fkit crazyit Charli

按照上面介绍的方法，while 循环也可用于遍历列表。

下面示范一个小程序，实现对一个整数列表的元素进行分类，能整除 3 的放入一个列表中；除以 3 余 1 的放入另一个列表中；除以 3 余 2 的放入第三个列表中：

```
src_list = [12, 45, 34,13, 100, 24, 56, 74, 109]
a_list = [] # 定义保存整除 3 的元素
b_list = [] # 定义保存除以 3 余 1 的元素
c_list = [] # 定义保存除以 3 余 2 的元素
# 只要 src_list 还有元素，继续执行循环体
while len(src_list) > 0:
    # 弹出 src_list 最后一个元素
    ele = src_list.pop()
    # 如果 ele % 2 不等于 0
    if ele % 3 == 0 :
        a_list.append(ele) # 添加元素
    elif ele % 3 == 1:
        b_list.append(ele) # 添加元素
    else:
        c_list.append(ele) # 添加元素
print("整除 3:", a_list)
print("除以 3 余 1:",b_list)
print("除以 3 余 2:",c_list)
```