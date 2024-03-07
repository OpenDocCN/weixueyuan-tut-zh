# Python try finally 语句的用法

> 原文：[`www.weixueyuan.net/a/821.html`](http://www.weixueyuan.net/a/821.html)

前面介绍了 except 分支和 else 分支，它们分别在有异常抛出和没有异常抛出时被执行。接下来介绍另一个分支，该分支在任何情况下都会被执行，而且是在最后被执行，这就是 finially 分支。

下面是 finally 分支的一个例子。

>>> def finally_demo(a, b):            # 定义一个包含异常处理的函数
...     try:
...             a = a / b
...     except ZeroDivisionError, except_obj:   # 捕捉被 0 除的异常
...             print("Exception Message = %s" % except_obj.message)
...     else:                          # 没有异常时会触发的分支
...             print("No Exception is Got")
...     finally:                       # 不论有无异常都被触发的分支
...             print("Finnally Branch is Running")
...
>>> finally_demo(12, 2)
No Exception is Got                    # finally 分支的输出，第 9 行的输出
Finnally Branch is Running
>>> finally_demo(12, 0)               
Exception Message = integer division or modulo by zero  # 第 5 行的输出
Finnally Branch is Running             # 第 9 行的输出

即使发生了异常但是没有被捕捉到，该分支还是会被执行。如下面的例子：

>>> def finally_demo(a, b):            # 定义函数
...     try:                           # 异常捕捉区
...             a = a / b                              
...     except EOFError, except_obj:   # 如果出现文件，则结束异常
...             print("Exception Message = %s" % except_obj.message)
...     else:                          # 如果没有出现该异常
...             print("No Exception is Got")
...     finally:                       # 不论是否出现异常，都要执行的分支
...             print("Finnally Branch is Running")
...                                    # 函数定义结束
>>> finally_demo(12, 4)                # 没有出现异常
No Exception is Got                    # else 分支会执行
Finnally Branch is Running             # finally 分支会执行
>>> finally_demo(12, 0)                # 抛出异常，但是没有被捕捉到
Finnally Branch is Running             # finally 分支仍然会被执行
Traceback (most recent call last):     # 异常信息
File "<stdin>", line 1, in <module>
File "<stdin>", line 3, in finally_demo  # 没有被捕捉的异常
ZeroDivisionError: integer division or modulo by zero

而且在正常分支中包含 return 语句并且没有引发异常，finnally 分支也会被执行。这和 else 分支是不同的，下面就是这样的一个例子。

>>> def div(a, b):                  # 定义一个除法函数
...     try:                        # 正常分支
...         c = a / b          
...         return c
...     except ZeroDivisionError as ex_obj:
...         print("Got Exception: %s" % ex_obj)
...     finally:
...         print(u"finnally 分支在执行")
...                      # 函数定义结束
>>> div(8, 2)
finnally 分支在执行        # finally 分支的输出
4                        # 结果，即返回值