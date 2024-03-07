# Python assert 断言函数及用法

断言语句和 if 分支有点类似，它用于对一个 bool 表达式进行断言，如果该 bool 表达式为 True，该程序可以继续向下执行；否则程序会引发 AssertionError 错误。

例如如下程序：

```
s_age = input("请输入您的年龄:")
age = int(s_age)
assert 20 < age < 80
print("您输入的年龄在 20 和 80 之间")
```

上面程序中粗体字代码断言 age 必须位于 20 到 80 之间。运行上面程序，如果输入的 age 处于执行范围之内，则可看到如下运行过程：

请输入您的年龄:23
您输入的年龄在 20 和 80 之间

如果输入的 age 不处于 20 到 80 之间，将可以看到如下运行过程：

请输入您的年龄:1
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 3, in <module>
    assert 20 < age < 80
AssertionError

从上面的运行过程可以看出，断言也可以对逻辑表达式进行判断，因此实际上断言也相当于一种特殊的分支。

assert 断言的执行逻辑是：

if 条件为 False：
    程序引发 AssertionError 错误