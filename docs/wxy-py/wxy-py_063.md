# Python readlines()函数：读取所有行的文本

> 原文：[`www.weixueyuan.net/a/811.html`](http://www.weixueyuan.net/a/811.html)

readlines() 函数将文件的每行作为一个元素，组合成一个列表返回。

参数 hint 用来限制读入的行数。如果参数 hint 为负数或者 0，表示没有行数的限制。hint 的默认值是 -1，即默认情况下是不对读入的行数进行限制的，返回的是文件的所有行。

下面的例子演示了没有指定 hint 值而使用默认值 -1 的情况，其会读出所有的行，并且将这些行组成一个列表返回。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> lines = fd.readlines()     # 将所有的行读出
>>> lines                      # 显示返回值，是一个列表，每个元素表示一行
['this is input text file\n', 'it contains 3 lines\n', 'this is the end
        of file\n']
>>> fd.close()                 # 关闭文件
```

如果 hint 为正数，则会依次读入各行，并检查读入的字符数是否大于 hint。如果大于或等于 hint，则停止继续读入下一行；如果小于 hint 则继续读入下一行。下面的例子演示了 hint 比实际文件字节数小的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> lines = fd.readlines(3)    # 如果读入字符数超过 3，就停止读入下一行
>>> lines                      # 所以只读入了一行便停止了
['this is input text file\n']
>>> lines = fd.readlines(30)   # 如果读入超过了 30 个字符，就停止读入下一行
>>> lines                      # 第二行字符串小于 30，所以继续读入第三行
['it contains 3 lines\n', 'this is the end of file\n']
>>> fd.close()                 # 关闭文件
```