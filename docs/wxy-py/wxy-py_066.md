# Python seek()函数：设定读写位置

> 原文：[`www.weixueyuan.net/a/815.html`](http://www.weixueyuan.net/a/815.html)

可以使用 seek() 函数调整当前读写文件的位置。例如，我们从文件头部读取了 1000 个字节，处理这 1000 个字节后发现还有一个很重要的信息在文件的第 3 个字节，此时可以将当前位置移动到相对头部 3 个字节的位置，这样下次 read() 操作便可以得到希望得到的信息。

该函数有 2 个参数，第一个参数是 offset，表示偏移量，可以为负数；第二个参数是 whence，表示相对于什么，可以是相对文件的头、文件的尾、当前文件。whence 的默认值是 0，表示文件头，其还可以为 1 表示当前位置，或者为 2 表示文件尾部。

下面的例子将打开文件，然后将当前位置移动到尾部的前 2 个字符，这样下次读到的就是文件最后面的两个字符。该文件的最后两个字符是 yz。这里需要注意的是，文件一定要以二进制模式打开，否则 seek() 会抛出异常。下面是完整代码：

```

try:
    # 必须以二进制模式打开文件，所以带上 b 这个标识
    file_obj1 = open(u"data.txt", "rb")
    pos = file_obj1.tell()
    print(u"1)当前位置为%d" % pos)
    file_obj1.seek(-2, 2)
    print(u"2)当前位置为%d" % pos)
    data = file_obj1.read(2)
    print(u"读出了%d 个字节" % len(data))
    print("data = [%s]" % str(data))
    pos = file_obj1.tell()
    print(u"3)当前位置为%d" % pos)
    file_obj1.close()
except IOError:
   # 找不到文件时提示文件不存在
   print(u"File not Exist")
```

运行该程序，可以得到如下输出结果：

E:\>python seekDemo1.py
1)当前位置为 0
2)当前位置为 0
读出了 2 个字节
data = [b'yz']
3)当前位置为 26

下面的例子使用 tell() 和 seek() 来得到某个文件的字节数。其方法是通过得到文件头部的位置信息 start_pos 和尾部的位置信息 stop_pos，求 stop_pos-start_pos 的值来得到文件的大小。代码如下：

```

try:
    file_obj1 = open(u"data.txt", "rb")
    file_obj1.seek(0, 2)
    end_pos = file_obj1.tell()
    print(u"文件大小为%d" % end_pos)
    file_obj1.close()
except IOError:
    # 找不到文件时提示文件不存在
    print(u"File not Exist")
```