# Python sorted 函数及用法

sorted() 函数与 reversed() 函数类似，该函数接收一个可迭代对象作为参数，返回一个对元素排序的列表。

在交互式解释器中测试该函数，可以看到如下运行过程：

>>> a = [20, 30, -1.2, 3.5, 90, 3.6]
>>> sorted(a)
[-1.2, 3.5, 3.6, 20, 30, 90]
>>> a
[20, 30, -1.2, 3.5, 90, 3.6]

从上面的运行过程来看，sorted() 函数也不会改变所传入的可迭代对象，该函数只是返回一个新的、排序好的列表。

在使用 sorted() 函数时，还可传入一个 reverse 参数，如果将该参数设置为 True，则表示反向排序。例如如下测试过程：

>>> sorted(a, reverse = True)
[90, 30, 20, 3.6, 3.5, -1.2]

在调用 sorted() 函数时，还可传入一个 key 参数，该参数可指定一个函数来生成排序的关键值。比如希望根据字符串长度排序，则可为 key 参数传入 len 函数。例如如下运行过程：

>>> b = ['fkit', 'crazyit', 'charlie', 'fox', 'Emily']
>>> sorted(b, key = len)
['fox', 'fkit', 'Emily', 'crazyit', 'charlie']

通过 sorted() 函数的帮助，程序可对可迭代对象按照由小到大的顺序进行遍历。例如如下程序：

```
my_list = ['fkit', 'crazyit', 'Charlie', 'fox', 'Emily']
for s in sorted(my_list, key=len):
    print(s)
```