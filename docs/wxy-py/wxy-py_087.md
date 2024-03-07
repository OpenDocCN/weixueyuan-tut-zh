# Python 静态方法的使用

> 原文：[`www.weixueyuan.net/a/653.html`](http://www.weixueyuan.net/a/653.html)

静态方法就是该方法没有绑定某个对象。和对象方法不同的是，其定义时第一个参数不是 self，这看起来更像一个普通方法。由于没有 self 参数，所以也不能使用“self.属性名”来访问某个对象的属性了。

静态方法的定义格式如下：

```

@staticmethod
def static_func():
    pass
```

在 Student 类中，得到最低成绩和最高成绩是比较适合用静态方法来实现的，因为这两个函数不需要操作学生对象的数据。如下面的代码所示。

>>> class Student:                       # 定义类 Student
...     highest_score = 0                # 类属性
...     lowest_score = 100
...     def __init__(self):              # 初始化函数
...         self.name = ""
...     @staticmethod                    # 定义静态方法，第一个参数不是 self
...     def get_highest_score():
...         return Student.highest_score
...     def set_score(self, score):      # 定义普通方法
...         if score > Student.highest_score:
...             Student.highest_score = score
...         if score < Student.lowest_score:
...             Student.lowest_score = score
...
>>> student_a = Student()
>>> student_a.set_score(98)              # 普通方法
>>> student_b = Student()
>>> student_b.set_score(90)
>>> student_c = Student()
>>> student_c.set_score(92)
>>> Student.get_highest_score()          # 静态方法
98