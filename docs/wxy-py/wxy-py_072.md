# Python json 模块：处理 json 文件

> 原文：[`www.weixueyuan.net/a/706.html`](http://www.weixueyuan.net/a/706.html)

json（JavaScript Object Notation）格式的文件多用于在计算机程序之间交换数据。和 yaml 不同的是，该格式并不使用友好的缩进来界定元素的边界，而是使用“{}”、“[]”、“,”和“:”等特殊字符来标识字段的分割。

和 yaml 类似，json 也包括以下三种数据类型：

1) 对象，对应到 Python 中的字典。下面是一个例子：

{1: 12, 2: 22}

2) 数组，对应到 Python 的列表。下面是一个例子：

[1, 2, 3, 4]

3) 标量，对应到 Python 的字符串、整数、浮点数、true、false、null 等。

本节介绍的 json 模块是 Python 自带的，不需要安装，不过在使用之前需要先将其引入，方式如下：

import json

该模块主要提供两个功能，一个是将 json 字符串转换成 Python 对象，一个是将 Python 对象转换成 json 字符串。下面先来看看将 Python 对象转换成 json 字符串的功能。

将 Python 对象转换成字符串的基本函数是 dumps()，该函数接收一个 Python 对象，输出一个 json 格式的字符串。下面是该函数的使用例子：

```

>>> import json                     # 引入 json 模块
>>> obj = ["a", "b", 12, 24.6]      # 输入的 Python 对象，是一个列表
>>> out = json.dumps(obj)           # 调用 dumps()函数
>>> type(out)                       # 输出是字符串
<class 'str'>
>>> out                             # 输出的内容
'["a", "b", 12, 24.6]'
```

除了 dumps() 函数之外，还有一个类似的函数 dump()，该函数直接将输出写入到文件中。其第二个参数是文件对象，并将输出内容写入到该文件对象中。下面是该函数的使用例子：

```

import json
fd = open("writeJsonDemo2.json", "w")   # 打开文件
obj = ["a", "b", 12, 24.6]
out = json.dump(obj, fd)                # 将 Python 对象进行转换后写入文件
fd.close()                              # 关闭文件
```

运行该脚本后的输出如下：

$ python writeJsonDemo2.py              # 运行脚本
$ cat writeJsonDemo2.json               # 查看输出文件的内容
["a", "b", 12, 24.6]

下面介绍如何将 json 字符串转换成 Python 对象。和 yaml 类似，json 模块也提供了两个函数，一个是以字符串为输入，一个是以文件对象为输入。首先介绍以字符串为输入的函数 loads()，该函数接收的输入是字符串，输出是一个 Python 对象。

```

>>> import json                             # 引入 json 模块
>>> out = json.loads('["item1", {"key1": ["item2", null, 1.0, 2]}]')   # 转换字符串
>>> type(out)                               # 输出对象是字典
<class 'list'>
>>> out                                     # 输出对象的内容
['item1', {'key1': ['item2', None, 1.0, 2]}]
```

以文件对象为输入的函数是 load()，该函数接收一个文件对象作为参数，返回一个字符串。下面的代码演示了该接口函数的使用情况：

```

import json                             # 引入 json 模块
fd = open("writeJsonDemo2.json", "r")   # 打开输入文件
out = json.load(fd)                     # 转换文件内容
fd.close()                              # 关闭文件
print("Type: %s" % type(out))           # 查看返回对象的类型
print(out)                              # 查看返回对象的内容
```

运行该脚本后的输出如下：

$ cat writeJsonDemo2.json               # 查看输入文件的内容
["a", "b", 12, 24.6]
$ python jsonReadDemo1.py               # 运行脚本
Type: <class 'list'>
['a', 'b', 12, 24.6]