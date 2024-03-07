# Python tell()函数：获得当前读取位置

> 原文：[`www.weixueyuan.net/a/813.html`](http://www.weixueyuan.net/a/813.html)

文件可以被看作是字节流或者字符流。第一个读入的字符是在该流的第 0 个位置上。如果读入了 5 个字符，则现在在该流的第 5 个位置上。

我们可以用 tell() 来得到当前位置，其返回一个整数。下面的例子演示了 tell() 的用法。

```

try:
    file_obj1 = open(u"data.txt", "r")
    pos = file_obj1.tell()              # 最开始，位置应该为 0
    print(u"1)当前位置为%d" % pos)
    data = file_obj1.read(5)            # 读出 5 个字节
    print(u"读出了%d 个字节" % len(data))
    pos = file_obj1.tell()
    print(u"2)当前位置为%d" % pos)
    file_obj1.close()
except IOError:
   # 找不到文件时提示文件不存在
   print(u"File not Exist")
```

执行该脚本，输出如下：

E:>python tellDemo1.py
1)当前位置为 0
读出了 5 个字节
2)当前位置为 5