# Python 类方法的使用

> 原文：[`www.weixueyuan.net/a/654.html`](http://www.weixueyuan.net/a/654.html)

类方法也不属于某个对象，所以其第一个参数也不为 self。但它属于某个类，所以其第一个参数一定是 cls。

```

@classmethod
def static_func(cls, 参数列表):
    pass
```

在使用时不需要指定第一个参数 cls，因为该函数有一个隐含的属性 __self__，它就是 cls 的值。

>>> class Student:                             # 定义类
...     highest_score = 0
...     lowest_score = 100
...     def __init__(self):                    # 初始化函数
...         self.name = ""
...     @classmethod                           # 类方法
...     def get_highest_score(cls):
...         print("cls = ", cls)
...         return Student.highest_score
...     def set_score(self, score):             # 普通成员函数
...         if score > Student.highest_score:
...             Student.highest_score = score
...         if score < Student.lowest_score:
...             Student.lowest_score = score
...                                       # 类定义结束
>>> student_a = Student()
>>> student_b = Student()
>>> student_c = Student()
>>> student_a.set_score(98)
>>> student_b.set_score(92)
>>> student_c.set_score(95)
>>> Student.get_highest_score()
('type = ', <class __main__.Student at 0x1047a7a78>)
98
>>> Student                                # 类对象
<class __main__.Student at 0x1047a7a78>    # 函数的一个属性 __self__ 就是类对象
>>> student_a.get_highest_score.__self__
<class __main__.Student at 0x1047a7a78>