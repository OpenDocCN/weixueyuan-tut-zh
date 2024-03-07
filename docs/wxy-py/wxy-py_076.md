# Python try else 语句的用法

> 原文：[`www.weixueyuan.net/a/820.html`](http://www.weixueyuan.net/a/820.html)

前面介绍的都是 except 分支，也就是异常分支。except 分支在发生异常时被执行。本节介绍的分支在没有抛出异常时才被执行，这就是 else 分支。

下面演示了这种用法。

>>> def except_demo(a, b):            # 定义一个包含异常处理的函数
...     try:
...             a = a / b
...     except ZeroDivisionError, except_obj:   # 捕捉被 0 除的异常
...             print("Exception Message = %s" % except_obj.message)
...     else:                         # 没有异常时会触发的 else 分支
...             print("No Exception is Got")
...
>>> except_demo(12, 0)                # 被 0 除了
        # 第 5 行的输出
Exception Message = integer division or modulo by zero 
>>> except_demo(12, 2)
No Exception is Got                   # 第 7 行的输出，在没有发生异常时执行

需要注意的是，如果没有捕捉到异常，而且在正常分支中直接使用 return 来返回，则 else 分支不会被执行。如下面的例子：

>>> def div(a, b):
...     try:
...         c = a / b
...         return c        # 这个很关键，导致 else 分支永远不会被执行
...     except ZeroDivisionError as ex_obj:
...         print(u"捕捉到了异常: %s" % ex_obj)
...     else:               # 该分支不会被执行，不论是否有异常抛出
...         print(u"没有捕捉到异常")
...
>>> div(8, 2)               # 没有抛出异常，else 分支也不会被执行
4.0
>>> div(8, 0)               # 抛出异常，else 分支不会被执行
捕捉到了异常: division by zero