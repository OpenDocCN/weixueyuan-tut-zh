# Python 函数的返回值

> 原文：[`www.weixueyuan.net/a/547.html`](http://www.weixueyuan.net/a/547.html)

和 C 语言、Java 语言不同，Python 并不要求返回值的类型是统一的。也就是说，某个函数的返回值可以是多种类型。

如下面的例子中，输入一个字符串，如果字符串是 name，那么返回字符串表示的是名字信息；如果输入的是 age，那么返回值是一个整数，用来表示年龄信息。

```

>>> def get_info(info_type):  # 定义函数
        if info_type == "name":
            return "alex"
        if info_type == "age":
            return 26

>>> get_info("name")  # 使用函数，得到名字信息
'alex'                # 返回值为名字
>>> get_info("age")   # 使用函数，得到名字信息
26                    # 返回值是年龄
```

多数函数是带有 return 语句的；如果没有 return 语句，返回值是 None。

```

>>> def no_ret_demo():  # 定义一个没有 return 语句的函数
        print("Demo a function without return statement")

>>> a = no_ret_demo()   # 使用该函数，并将返回值保存到变量 a 中
Demo a function without return statement
>>> a is None           # 检查 a 是否为 None
True                    # 返回值是 None
```