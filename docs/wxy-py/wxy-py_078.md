# Python raise 语句：主动抛出异常

> 原文：[`www.weixueyuan.net/a/642.html`](http://www.weixueyuan.net/a/642.html)

通常情况下，我们的代码不会主动抛出异常，而是通过返回一个错误码来告知调用者这里出现了不该出现的错误。其实我们也可以在代码中抛出异常，通过异常将相关的错误信息发送出去。

可以使用 raise 语句来抛出异常，该语句后面需要带一个对象，该对象必须是派生自 BaseException。基本语法如下：

raise 派生自 BaseException 的对象

下面就是一个代码主动抛出异常的例子，其在检测到参数 b 为 0 时就抛出异常。

>>> def finally_demo(a, b):
...     if b == 0:
...             raise Exception('my personal exception')    # 抛出异常
...
>>> try:                      # 捕捉异常
...     finally_demo(12, 0)
... except Exception, e:      # 捕捉任意异常
...     print("Exception Message: %s" % e.message)   # 显示异常信息
...                           # 结束异常捕捉
Exception Message: my personal exception             # 第 8 行的输出

就像前面介绍的那样，抛出的对象必须继承自 BaseException，否则 raise 自身会抛出异常。下面的例子就演示了这种情况。

>>> raise 8     # 对象 8 不是继承自 BaseException，抛出 TypeError 异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: exceptions must derive from BaseException
>>> raise Exception('my personal exception')   # 抛出 Exception 异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
Exception: my personal exception

这里需要强调一下异常对象的继承关系，所有系统定义的异常对象都继承自 Exception，而 Exception 继承自 BaseException。所以如果自定义一个异常类型也是可以的，只需要继承自 Exception 即可。

下面是一个使用自定义异常的例子，涉及类的使用。类的使用需要到后面的章节才会详细介绍，这里简单了解即可。完整代码如下：

>>> class UserdefException(Exception):     # 自定义的异常类
...     def __init__(self, msg):
...         self.msg = msg
...                                              # 定义结束
>>> try:                                         # 进入捕捉区
...     raise UserdefException("experiment")     # 抛出异常
... except UserdefException as ex_obj:           # 捕捉到该异常
...     print(u"子定义异常,错误信息: %s" % ex_obj)
...                                  # 捕捉区结束
子定义异常,错误信息: experiment        # 第 8 行输出的信息