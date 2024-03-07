# Python bytes 类型（字节流）

> 原文：[`www.weixueyuan.net/a/426.html`](http://www.weixueyuan.net/a/426.html)

bytes 从名字上即可看出，其表示字节数组，每个单元表示单个字节。该类型字符串的表示方法和 str 类型类似，不过需要在前面加上`b`，如说下面的代码就是用单引号表示的 bytes 字符串。

```
>>> a = b'abc'  # 以字符 b 开头，后面跟着用单引号表示的 bytes 字符串
>>> type(a)
<type bytes>
```

当然也可以使用双引号或者三引号，下面是这两种表示方法的例子：

```
>>> b"python"              # 双引号
'python'
>>> b"""I love python      # 三个双引号
... do you like it?"""
'I love python\ndo you like it?'
>>> a = '''I love python   # 三个单引号
... do you love it?'''
>>> a
'I love python\ndo you love it?'
```

需要注意的是，这时不能使用中文字符，因为中文字符无法用单个 0～255 之间的值来表示。另外可以使用“\数值”来表示单个字符，如 123 表示 S，如下面所示：

```
>>> b'\123'          # 123 是字符 S 的 ASCII 码
b'S'
>>> b'\141\142\143'  # 141 是字符 a 的 ASCII 码
b'abc'
```