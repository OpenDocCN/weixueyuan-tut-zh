# Python read()函数：读入指定长度的文本

> 原文：[`www.weixueyuan.net/a/809.html`](http://www.weixueyuan.net/a/809.html)

read() 函数从当前位置开始读，读出指定个数的字符。其返回值是一个字符串，表示读取的文件内容。

参数 size 如果为正数，表示最多读出 size 个字符；如果 size 为 0，则什么也不会读出，返回值是空字符串；如果 size 为负数，表示读出全部的内容。size 的默认值是 -1，表示读出全部的内容。

下面的例子演示了不指定 size 的值而使用默认值 -1 的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> ret_str = fd.read()        # 将所有文件内容读到 ret_str，size=-1
>>> type(ret_str)              # 返回值类型是字符串
<class 'str'>
>>> len(ret_str)               # 字符串长度为 68
68
>>> print(ret_str)             # 显示文件内容
this is input text file
it contains 3 lines
this is the end of file
>>> fd.close()                 # 关闭文件
```

下面演示指定 size，并且 size 为正数的情况。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> str1 = fd.read(40)         # 读入最多 40 个字符，保存到 str1 中
>>> str2 = fd.read(40)         # 读入最多 40 个字符，保存到 str2 中
>>> len(str1)                  # str1 包含 40 个字符
40
>>> len(str2)                  # str2 包含 28 个字符，总共是 68 个字符
28
>>> print(str1+str2)           # 将 str1 和 str2 连接起来，就是文件的完整内容
this is input text file
it contains 3 lines
this is the end of file
>>> fd.close()                 # 关闭文件
```

如果到了文件的尾部，则返回空字符串。

```

>>> fd = open("in.dat", "r")   # 以只读方式打开文件 in.dat
>>> str1 = fd.read()           # 读出全部内容
>>> str2 = fd.read()           # 这时已经到了文件的尾部
>>> type(str2)                 # 返回值类型是字符串
<class 'str'>    
>>> len(str2)                  # str2 的长度为 0，所以是空字符串
0
>>> fd.close()                 # 关闭文件
```