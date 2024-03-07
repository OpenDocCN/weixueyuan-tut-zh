# Python __iter__ 和 __reversed__：实现迭代器

前面介绍了使用 for 循环遍历列表、元姐和字典等，这些对象都是司法代的，因此它们都属于迭代器。

如果开发者需要实现法代器，只要实现如下两个方法即可：

*   __iter__(self)：该方法返回一个法代器（iterator），迭代器必须包含一个 __next__()方法，该方法返回迭代器的下一个元素。
*   __reversed__(self)：该方法主要为内建的 reversed()反转函数提供支持，当程序调用 reversed() 函数对指定迭代器执行反转时，实际上是由该方法实现的。

从上面介绍不难看出，如果程序不需要让迭代器反转迭代，其实只需要实现第一个方法即可。下面程序将会定义一个代表斐波那契数列（数列的元素等于前两个元素之和：`f(n+2) = f(n+1) ＋f(n)`）的迭代器。

```
# 定义一个代表斐波那契数列的迭代器
class Fibs:
    def __init__(self, len):
        self.first = 0
        self.sec = 1
        self.__len = len
    # 定义迭代器所需的 __next__ 方法
    def __next__(self):
        # 如果 __len__ 属性为 0，结束迭代
        if self.__len == 0:
            raise StopIteration
        # 完成数列计算：
        self.first, self.sec = self.sec, self.first + self.sec
        # 数列长度减 1
        self.__len -= 1
        return self.first
    # 定义 __iter__ 方法，该方法返回迭代器
    def __iter__(self):
        return self
# 创建 Fibs 对象
fibs = Fibs(10)
# 获取迭代器的下一个元素
print(next(fibs))
# 使用 for 循环遍历迭代器
for el in fibs:
    print(el, end=' ')
```

上面程序定义了一个 Fibs 类，该类实现了 __iter__() 方法，该方法返回 self，因此它要求该类必须提供 __next__() 方法，该方法会返回数列的下二个值。程序使用 len 属性控制数列的剩余长度，当 __len 为 0 时，程序停止遍历。

上面程序创建了一个长度为 10 的数列，程序开始使用内置的 next() 函数来获取迭代器的下一个元素，该 next() 函数其实就是通过迭代器的 __next()__ 方法来实现的。

程序接下来使用 for 循环来遍历应数列。运行该程序，将看到如下输出结果：

1 1 2 3 5 8 13 21 34 55

此外，程序可使用内置的 iter() 函数将列表、元组等转换成迭代器，例如如下代码：

```
# 将列表转换为迭代器
my_iter = iter([2, 'fkit', 4])
# 依次获取迭代器的下一个元素
print(my_iter.__next__()) # 2
print(my_iter.__next__()) # fkit
```