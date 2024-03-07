# Python try except 语句：捕获异常

> 原文：[`www.weixueyuan.net/a/819.html`](http://www.weixueyuan.net/a/819.html)

捕捉异常就是如果某个代码段中出现了异常，需要定义好相应的异常处理代码。这样异常就被处理掉了，而不是一直向上传递，直到最后导致整个程序退出。

下面介绍几种捕捉异常的方法。

#### 1) 捕捉任意异常

最简单的处理方式是捕捉任意异常，方法是使用下面的分支语句：

except:

该分支会捕捉任意没有被捕捉到的异常。

>>> try:                                                              
...     a = 12 / 0                     # 正常分支
... except:                            # 异常处理分支
...     print("Got and exception")     # 异常分支处理函数
...                                                  # 所有异常分支结束了
Got and exception                      # 第 4 行代码执行的结果

#### 2) 捕捉特定类型的异常

当然也可以捕捉特定的异常，就是只对特定的异常进行处理，其他异常要么被更高阶的代码捕捉到，要么导致整个程序退出。

>>> try:                           # 异常捕捉区
...     a = 12 / 0
... except ZeroDivisionError:      # 仅捕捉 ZeroDivisionError 类型的异常
...     print("Divided By Zero")
...                                            # 异常分支结束
Divided By Zero                    # 第 4 行的输出

如果抛出的异常和捕捉的类型不一致，那么不会被捕捉到。

>>> try:                                  # 异常捕捉区
...     a = non_exist_var                 # 使用了不存在的对象
... except ZeroDivisionError:             # 仅捕捉被 0 除的异常
...     print("Divided By Zero")
...                                       # 所有的异常处理分支结束
Traceback (most recent call last):        # 由于没有被处理，导致程序结束
  File "<stdin>", line 2, in <module>
NameError: name 'non_exist_var' is not defined

可以通过捕捉 Expection 类型的异常对象来达到捕捉任意类型异常的目的。由于 Exception 可以匹配任意的异常，所以不用担心异常遗漏到高层的代码。最常见的用法是在前面对某些特定的异常进行处理，在最后放一个捕捉任意异常的分支来处理那些所有漏掉的异常。如下面的例子：

>>> def div(a, b):                            # 定义除法函数
...     try:                                                                            # 进入异常捕捉区
...         c = a / b
...         return c
...     except ZeroDivisionError as ex_obj:   # 被 0 除的异常
...         print(u"被 0 除的异常,错误信息: %s" % ex_obj)
...     except Exception as e:                # 其他所有的异常
...         print(u"不知道的异常, 错误信息: %s" % e)
...
>>> div(20, 0)                                # 被第 5 行捕捉到
被 0 除的异常,错误信息: division by zero
>>> div("abc", 0)                             # 被第 7 行捕捉到
不知道的异常, 错误信息: unsupported operand type(s) for /: 'str' and 'int'

#### 3) 捕捉多个异常

可以定义多个捕捉语言，用来捕捉不同的异常。这样可以对不同的异常进行不同的处理。

>>> def try_demo(x, y, z):
...     try:                                   # 异常捕捉区
...             a = x[y] / z
...     except ZeroDivisionError:              # 捕捉被 0 除的异常
...             print("Divided By Zero")       
...     except IndexError:                     # 捕捉下标错误
...             print("Index Error")
...                                            # 异常捕捉区结束
>>> try_demo([1, 2], 3, 3)
Index Error                                    # 第 7 行的输出
>>> try_demo([1, 2], 1, 0)
Divided By Zero                                # 第 5 行的输出

这里要注意的是，一个异常对象只能被捕捉到一次。如果有两个异常处理分支都可以处理该类型异常，那么前面的那个分支被触发，后面的不会被触发。这也是为何 Exception 分支要放到最后的缘故，因为它可以捕捉到任意异常，如果放在最前面，其他异常处理分支就永远也不会被触发。如下面的例子：

>>> def div(a, b):                         # 定义除法函数
...     try:                                                                            # 进入异常捕捉区
...         c = a / b
...         return c
...     except Exception:                   # 它会捕捉任意异常
...         print(u"捕捉得到 Exception 类型的异常") # 它没有机会捕捉任意异常，因为前面的分支已经捕捉了所有异常
...     except ZeroDivisionError :
...         print(u"捕捉得到 ZeroDivisionError 类型的异常")
...
>>> div(8, 0)
捕捉得到 Exception 类型的异常                    # 第 6 行的输出

还有一个异常类型，即 BaseException，Exception 即派生自该类型，所以该类型和 Exception 一样，也可以捕捉一切类型的异常。下面是将 BaseException 放在最前面的情况，其会屏蔽所有的捕捉分支。

>>> def div(a, b):
...     try:
...         c = a / b
...         return c
...     except BaseException:
...         print(u"捕捉得到 BaseException 类型的异常")
...     except ZeroDivisionError :
...         print(u"捕捉得到 ZeroDivisionError 类型的异常")
...
>>> div(8, 0)          # 被 0 除的异常 ZeroDivisionError，被第 5 行的分支捕捉
捕捉得到 BaseException 类型的异常
>>> div("abc", 2)      # 类型错误异常 TypeError，也被第 5 行的分支捕捉
捕捉得到 BaseException 类型的异常

#### 4) 得到异常对象

也可以得到捕捉到的异常对象，这样便可以获得更多异常发生时的信息。

>>> try:                    # 异常捕捉区
...     a = 12 / 0          # 被 0 除的操作
... except ZeroDivisionError as except_obj:
...     print("Got Expection")
...     print("Exception Message = %s", except_obj.message)
...                         # 异常捕捉区结束
Got Expection
('Exception Message = %s', 'integer division or modulo by zero')