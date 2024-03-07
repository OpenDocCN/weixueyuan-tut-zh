# Python 字符串大小写转换（3 种）函数及用法

Python 字符串由内建的 str 类代表，那么 str 类包含哪些方法呢？Python 非常方便，它甚至不需要用户查询文档，Python 是“自带文档”的。

这里需要读者简单掌握两个帮助函数：

1.  dir()：列出指定类或模块包含的全部内容（包括函数、方法、类、变量等）。
2.  help()：查看某个函数或方法的帮助文档。

例如，要查看 str 类包含的全部内容，可以在交互式解释器中输入如下命令：

>>> dir(str)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
>>>

上面列出了 str 类提供的所有方法，其中以“_”开头、“_”结尾的方法被约定成私有方法，不希望被外部直接调用。

如果希望查看某个方法的用法，则可使用 help() 函数。例如，在交互式解释器中输入如下命令：

>>> help(str.title)
Help on method_descriptor:

title(...)
    S.title() -> str

    Return a titlecased version of S, i.e. words start with title case
    characters, all remaining cased characters have lower case.

>>>

从上面介绍可以看出，str 类的 title() 方法的作用是将每个单词的首字母大写，其他字母保持不变。

在 str 类中与大小写相关的常用方法如下：

*   title()：将每个单词的首字母改为大写。
*   lower()：将整个字符串改为小写。
*   upper()：将整个字符串改为大写。

例如，如果希望看到 lower() 方法的相关用法，可运行如下命令：

>>> help(str.lower)
Help on method_descriptor:

lower(...)
    S.lower() -> str

    Return a copy of the string S converted to lowercase.

>>>

如下代码示范了 str 的与大小写相关的常用方法：

```
a = 'our domain is crazyit.org'
# 每个单词首字母大写
print(a.title())
# 每个单词首字母小写
print(a.lower())
# 每个单词首字母大写
print(a.upper())
```

运行上面程序，可以看到如下输出结果：

Our Domain Is Crazyit.Org
our domain is crazyit.org
OUR DOMAIN IS CRAZYIT.ORG