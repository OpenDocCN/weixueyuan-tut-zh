# Python write()和 writelines()函数：写入文本

> 原文：[`www.weixueyuan.net/a/812.html`](http://www.weixueyuan.net/a/812.html)

在 Python 3 中，write() 函数的返回值是参数 data 的字节数。在 Python 2 中，其返回值是 None。下面演示了这种不同：

```

>>> fd = open("out.dat", "w")  # Python 3 中的情况
>>> fd.write("line 1")         # 写入字符串，返回值是字符的个数
6
>>> fd.close()
5
>>> fd = open("out.dat", "w")  # Python 2 中的情况下打开文件
>>> fd.write("line 1")         # 写入字符串，返回值为 None
>>> fd.close()
```

writelines(lines) 函数的功能是写入多行，其中 lines 是一个列表或者元组，执行效果相当于是 write(''.join(lines))，各行之间并不会填充任何数据。

```

>>> fd = open("out.dat", "w")                  # 打开文件 out.dat
>>> fd.writelines(["line1", "line2", "line3"]) # 写入字符串列表
>>> fd.close()                     # 关闭文件
>>> fdr = open("out.dat", "r")     # 查看写入的内容
>>> fdr.read()        # 读入文件内容
'line1line2line3'     # 行与行之间是没有添加任何信息的
>>> fdr.close()       # 关闭 fdr
>>> fd = open("out.dat", "w")                  # 打开文件 out.dat
>>> fd.writelines(("line1", "line2", "line3")) # 写入字符串元组
>>> fd.close()          # 关闭文件
>>> fdr = open("out.dat", "r")                 # 检查写入的数据
>>> fdr.read()                                 # 可以发现没有添加数据在行之间
'line1line2line3'
>>> fdr.read()
```