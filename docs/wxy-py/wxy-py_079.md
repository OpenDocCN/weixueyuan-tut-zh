# Python 常见的异常类型汇总

> 原文：[`www.weixueyuan.net/a/643.html`](http://www.weixueyuan.net/a/643.html)

本节将介绍常见的系统定义的异常类。以后如果看到这些对象被抛出，便可以大致明白出现了什么情况。

比如，如果在代码运行的日志中看到下面的异常：

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for /: 'str' and 'float'

那么从 TypeError 异常类型大致便可以知道应该是某个变量类型是错误的，可以通过查看相关对象的类型来确认该问题的原因。

下面是一些比较常见的系统异常类型：
1) BaseException：所有异常的基类。

2) AttributeError：访问不存在的属性。

>>> a = 12
>>> a.time                                                                     # 整数没有 time 属性
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'int' object has no attribute 'time'

3) AssertionError：assert 断言语句失败。

>>> a = 2
>>> assert a < 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AssertionError

4) EOFError：到了文件的尾部了。假定打开一个文件，然后循环进行读操作，当读到文件尾部时就会抛出这个异常。在 Windows 中，按住 Ctrl+D 便会触发该异常。

>>> input("please input your name >>>")
        # 按住 Ctrl+D
please input your name >>>Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
EOFError

5) IndentationError：对齐错误。Python 要求代码块缩进对齐，如果发现没有对齐会抛出该异常。

>>>    m=12               # m 的前面多了很多个空格，导致对齐异常
  File "<stdin>", line 1
    m=12
    ^
IndentationError: unexpected indent

6) IndexError：错误的位置，如超出了元素位置范围。

>>> list_obj1 = []                             # 空的列表
>>> list_obj1[10]                              # 不存在第 11 个元素
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range

7) KeyError：访问了不存在的键。

>>> dict_obj1 = {}                             # 创建一个空的字典
>>> dict_obj1['a']                             # 没有'a'这个键
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'a'

8) KeyboardInterrupt：当在键盘上按住 Ctrl+C 时会触发该异常。

>>>             # 按住 Ctrl+C
KeyboardInterrupt

9) ModuleNotFoundError

>>> import nonexistmodule          # 引入不存在的模块
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'nonexistmodule'

10) NameError：使用了没有定义的对象。

>>> m                          # 不存在的变量
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'm' is not defined

11) RecursionError：递归深度超过设定的最大值。

>>> def demo(a):                # 递归函数，递归深度等于 a 的值
...     if a > 0:
...         return demo(a-1)
...     else:
...         return a
...
>>> demo(10000)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in demo
  File "<stdin>", line 3, in demo
  File "<stdin>", line 3, in demo
  [Previous line repeated 995 more times]
  File "<stdin>", line 2, in demo
RecursionError: maximum recursion depth exceeded in comparison

12) SyntaxError：语法错误。

>>> a = 12
>>> a--
  File "<stdin>", line 1
    a--
      ^
SyntaxError: invalid syntax

13) TypeError：数据类型错误。如除法操作符要求两边的操作数都是数字，不能是字符串，否则就抛出 TypeError。

>>> "abc" / 12                                 # 被除数的类型错误
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for /: 'str' and 'int'
>>> 12 / "abc"                                 # 除数的类型错误
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for /: 'int' and 'str'

14) ZeroDivisionError：被 0 除。

>>> 12 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero

15) IOError：I/O 操作错误，如打开不存在的文件。

>>> def io_except_demo():
...     try:    # 打开一个不存在的文件
...             fd = open("non_exist_file.txt", "r")
...     except IOError, except_obj:
...             print("Got IOError Exception")
...     else:
...             print("No Exception is Got")    # 如果没有异常，则执行该分支
...
>>> io_except_demo()
Got IOError Exception         # 第 5 行的输出