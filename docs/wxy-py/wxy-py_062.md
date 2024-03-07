# Python readline()函数：读取一行文本

> 原文：[`www.weixueyuan.net/a/810.html`](http://www.weixueyuan.net/a/810.html)

readline()函数的功能是读入一行数据，所以文件类型要求是文本，不能是二进制。返回值是这行数据，包括尾部的换行符；如果没有数据则返回一个空的字符串。该函数也带有参数 size，表示的含义和 read() 一样。size 的默认值也是 -1，表示读取完整的一行。

下面的例子演示了没有指定 size 而使用默认值 -1 的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> line = fd.readline()       # 读入一行
>>> while line:                # 如果不是空行，就是还没有到尾部
...     print(line, end='')    # 打印读入的行，关闭自动换行功能
...     line = fd.readline()   # 读入下一行
...                            # 结束 while 循环
this is input text file        # 显示的文件内容
it contains 3 lines
this is the end of file
>>> fd.close()                 # 关闭文件
```

下面的例子演示了 size 为正数的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> line = fd.readline(2)      # 读入一行，但最多读入 2 个字符
>>> len(line)                  # 返回字符串的长度为 2
2
>>> line                       # 显示返回值的内容
'th'
>>> fd.close()                 # 关闭文件
```

下面的例子演示了 size 为 0 的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> line = fd.readline(0)      # 读入一行，但是最多只能读入 0 个字符
>>> len(line)                  # 返回的是空字符串
0
>>> fd.close()                 # 关闭文件
```