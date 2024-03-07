# Python for 循环及用法详解

for-in 循环专门用于遍历范围、列表、元素和字典等可迭代对象包含的元素。

for-in 循环的语法格式如下：

for 变量 in 字符串｜范围｜集合等：
    statements

对于上面的语法格式有以下两点说明：

1.  for-in 循环中的变量的值受 for-in 循环控制，该变量将会在每次循环开始时自动被赋值，因此程序不应该在循环中对该变量赋值。
2.  for-in 循环可用于遍历任何可选代对象。所谓可迭代对象，就是指该对象中包含一个 __iter__ 方法，且该方法的返回值对象具有 next() 方法。

for-in 循环可用于遍历范围。例如，如下程序使用 for-in 循环来计算指定整数的阶乘：

```
s_max = input("请输入您想计算的阶乘:")
mx = int(s_max)
result = 1
# 使用 for-in 循环遍历范围
for num in range(1, mx + 1):
    result *= num
print(result)
```

上面程序将会根据用户输入的数字进行循环。假如用户输入 7，此时程序将会构建一个 range(1,8) 对象（不包含 8），因此 for-in 循环将会自动循环 7 次，在每次循环开始时，num 都会被依次自动赋值为 range 所包含的每个元素。

for-in 循环中的变量完全接受 for-in 循环控制，因此该变量也被称为循环计数器。

运行上面程序，如果输入 7，将会看到如下运行过程：

请输入您想计算的阶乘:7
5040

程序对 for-in 循环的循环计数器赋值在语法上是允许的，但没有什么意义，而且非常容易导致错误。例如如下程序：

```
for i in range(1, 5):
    i= 20
    print("i: ",i);
```

上面程序中的粗体字代码对循环计数器 i 赋值，这样导致程序每次循环时都要先对变量 i 赋值，当程序刚进入循环体时，i 就被重新赋值为 20，因此在循环体中看到的 i 永远是 20。运行上面程序，将看到如下输出结果：

i:  20
i:  20
i:  20
i:  20

## for-in 循环遍历列表和元组

在使用 for-in 循环遍历列表和元组时，列表或元组有几个元素，for-in 循环的循环体就执行几次，针对每个元素执行一次，循环计数器会依次被赋值为元素的值。

如下代码使用 for-in 循环遍历元组：

```
a_tuple = ('crazyit', 'fkit', 'Charlie')
for ele in a_tuple:
    print('当前元素是:', ele)
```

当然，也可按上面方法来遍历列表。例如，下面程序要计算列表中所有数值元素的总和、平均值：

```
src_list = [12, 45, 3.4, 13, 'a', 4, 56, 'crazyit', 109.5]
my_sum = 0
my_count = 0
for ele in src_list:
    # 如果该元素是整数或浮点数
    if isinstance(ele, int) or isinstance(ele, float):
        print(ele)
        # 累加该元素
        my_sum += ele
        # 数值元素的个数加 1
        my_count += 1
print('总和:', my_sum)
print('平均数:', my_sum / my_count)
```

上面程序使用 for-in 循环遍历列表的元素，并对几何元素进行判断：只有当列表元素是数值（int、float）时，程序才会累加它们，这样就可以计算出列表中数值元素的总和。

上面程序使用了 Python 的 isinstance() 函数，该函数用于判断某个变量是否为指定类型的实例，其中前一个参数是要判断的变量，后一个参数是类型。我们可以在 Python 的交互式解释器中测试该函数的功能。例如如下运行过程：

>>> isinstance(2,int)
True
>>> isinstance('a',int)
False
>>> isinstance('a',str)
True

从上面的运行过程可以看出，使用 isinstance() 函数判断变量是否为指定类型非常方便、有效。

如果需要，for-in 循环也可根据索引来遍历列表或元组：只要让循环计数器遍历 0 到列表长度的区间，即可通过该循环计数器来访问列表元素。例如如下程序：

```
a_list = [330, 1.4, 50, 'fkit', -3.5]
# 遍历 0 到 len(a_list)的范围
for i in range(0, len(a_list)) :
    # 根据索引访问列表元素
    print("第%d 个元素是 %s" % (i , a_list[i]))
```

运行上面程序，可以看到如下输出结果：

第 0 个元素是 330
第 1 个元素是 1.4
第 2 个元素是 50
第 3 个元素是 fkit
第 4 个元素是 -3.5

## for-in 循环遍历字典

使用 for-in 循环遍历字典其实也是通过遍历普通列表来实现的。前面在介绍字典时己经提到，字典包含了如下三个方法：

*   items()：返回字典中所有 key-value 对的列表。
*   keys()：返回字典中所有 key 的列表。
*   values()：返回字典中所有 value 的列表。

因此，如果要遍历字典，完全可以先调用字典的上面三个方法之一来获取字典的所有 key-value 对、所有 key、所有 value，再进行遍历。如下程序示范了使用 for-in 循环来遍历字典：

```
my_dict = {'语文': 89, '数学': 92, '英语': 80}
# 通过 items()方法遍历所有 key-value 对
# 由于 items 方法返回的列表元素是 key-value 对，因此要声明两个变量
for key, value in my_dict.items():
    print('key:', key)
    print('value:', value)
print('-------------')
# 通过 keys()方法遍历所有 key
for key in my_dict.keys():
    print('key:', key)
    # 在通过 key 获取 value
    print('value:', my_dict[key])
print('-------------')
# 通过 values()方法遍历所有 value
for value in my_dict.values():
    print('value:', value)
```

上面程序通过三个 for-in 循环分别遍历了字典的所有 key-value 对、所有 key、所有 value。尤其是通过字典的 items() 遍历所有的 key-value 对时，由于 items() 方法返回的是字典中所有 key-value 对组成的列表，列表元素都是长度为 2 的元组，因此程序要声明两个变量来分别代表 key、value（这也是序列解包的应用）。

假如需要实现一个程序，用于统计列表中各元素出现的次数。由于我们并不清楚列表中包含多少个元素，因此考虑定义一个字典，以列表的元素为 key，该元素出现的次数为 value。程序如下：

```
src_list = [12, 45, 3.4, 12, 'fkit', 45, 3.4, 'fkit', 45, 3.4]
statistics = {}
for ele in src_list:
    # 如果字典中包含 ele 代表的 key
    if ele in statistics:
        # 将 ele 元素代表出现次数加 1
        statistics[ele] += 1
    # 如果字典中不包含 ele 代表的 key，说明该元素还未出现国
    else:
        # 将 ele 元素代表出现次数设为 1
        statistics[ele] = 1       
# 遍历 dict，打印出各元素的出现次数
for ele, count in statistics.items():
    print("%s 的出现次数为:%d" % (ele, count))
```